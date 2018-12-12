---
title: WiscKey: Separating Keys from Values in SSD-conscious Storage
tags: database
---

越来越多的存储引擎采用了LSM-tree这一数据结构，如leveldb, rocksdb等等。相对于B-tree, LSM-tree将随机写转化为顺序写，从而提高了写的性能；作为代价，LSM-tree牺牲了一定的读性能，由于顺序写，无法保持key的相对结构，那么读的时候就需要按时间序从新到旧遍历直到读到该key；另一方面，由于顺序写入的时候不会删除旧值，这也导致LSM-tree中仍然保留着一些已经被覆盖或者删除的value，不利于快速查找到key；为了优化读性能，LSM-tree通过后台线程进行排序合并，使得key总体有序并删除无效值。但是这样反过来又导致了LSM-tree写放大的问题，因为要进行排序合并，势必要读取旧文件，写入排序后的新文件，特别是当LSM-tree比较接近容量上限时，几乎每次写入都会触发合并，并且合并可能涉及每个level。

WiscKey提出了一种基于kv分离的优化LSM-tree写放大的方法。注意题目中的SSD-conscious，WiscKey利用了一些SSD的特性来做优化，对HDD不适用。

WiscKey对于写放大的归因是直接的，写放大的直接原因是LSM-tree某个level的容量到达上限，就会触发合并，导致写入性能下降。那么为了减少写放大，减少LSM-tree每层的数据量，使其不轻易触发合并即可。LevelDB等实现中直接将kv一起存储在LSM-tree中，但是LSM-tree的排序合并是只与key有关，而与value完全无关的；另外，实际应用中大部分场景下，key都是相对较短而且长度相对稳定的，value的长度则可能方差较大。WiscKey的优化就是将key-value分开存储，LSM-tree中只存储key，value存到另外的文件(称为vLog)中，这样一来，LSM-tree中数据量较少，不易发生合并，即使发生合并，由于层次较低，写放大也不会太严重。

KV分离后，读写逻辑相应要做变更。

首先是写，由于KV分离，WiscKey需要先将value写入vLog(append)，之后将<key, value-addr, value-size>写入LSM-tree。这里多了一次顺序写，但是由于LSM-tree存储的数据量较少，层次较少，这多出来的一次顺序写相比KV不分离情况下发生合并的写放大系数小很多。

再看读，同样的，WiscKey需要先从LSM-tree读取<key, value-addr, value-size>，再从vLog中读取value。这里比LevelDB多了一次随机读操作，但是由于LSM-tree存储的数据变少，内存中可以cache更多level，读性能的损失主要来自于随机读。与HDD相比，SSD的随机读性能与顺序读的差距没有那么大，HDD随机读性能差主要在于磁头的机械移动。

最后再看范围查询(iterate/range query)，LevelDB中由于KV是按key有序存在一起，范围查询实际上是顺序读；而在WiscKey中，由于KV分离，范围查询变成多次随机读，整体的性能损失就比较大了。为了优化range query的性能，WiscKey利用SSD的并行性，预取start-key之后一定数量的key-value。

以上是对LSM-tree原有接口的逻辑调整。容易发现，vLog中无效的value未被清理，这会导致vLog占用较多空间，仍然需要对vLog进行清理(garbage collection)。WiscKey在vLog中不仅存储了value，也存了key，这样可以遍历vLog，然后查询LSM-tree中该key当前value的address，从而判断vLog中的value是否应该被删除。WiscKey保持vLog中的有效数据在一个连续的空间[head, tail)上，后台线程从head处读取一批key-value pair，筛选出有效value append到tail并更新LSM-tree中的value-addr，最后更新head/tail并持久化(head, tail与原文含义相反)。为保证crash consistency，这里的执行顺序是，先在tail append valid values，调用fsync；更新tail；更新LSM-tree中的value-addr；最后持久化新的head。

最后，有关于crash consistency，LevelDB中因为kv连续存储，能够保证kv写入的原子性及按写入顺序恢复(异步写/批量写)，但是在WiscKey中KV分离后不能保证这一点。对于value成功写入vLog，而key写入LSM-tree前崩溃的情况，无需特殊处理，无效的value会被gc清理。而对于key成功写入，但是value写入失败的情况，在读取时需要check value-addr是否在vLog有效区间内，以及key是否匹配。(如果在value写入后fsync是否可以避免这种情况???)

WiscKey还提出了一些其它优化，首先是vLog write buffer，因为每次写都要write vLog，可以在内存中buffer一段value，批量写入vLog；问题是crash后数据会丢失。另外一个优化是可以不需要WAL，因为vLog中已经存了完整的key-value。

原文中还有大量可供参考的数据，这里就不展示了。具体场景的优化情况，仍然需要详细的测试数据说明。
