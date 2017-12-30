---
title: end-to-end arguments in system design
tags: system design
---

本文是系统设计领域著名论文，End-to-end arguments in system design，的中文翻译。

\[系统设计中的端到端原则\]

本文提出了一个设计原则，指导分布式系统中如何在模块间分配功能。这一原则，称为端到端原则，指一些功能在系统低层提供与其成本相比可能是多余的或者价值不大的。本文讨论了一些例子，包括位错误恢复，加密，消息去重，系统故障恢复，确认传输。支持这些功能的低层机制只有性能扩展。

# 引言

# release assert
如果我们想要在release版本中使用assert怎么办。一种方案是
```python
#undef NDEBUG
#include <cassert>
```
还有一种我觉得更好的是使用glog提供的CHECK宏，具体有：

1. 判断大小关系，CHECK_EQ, CHECK_NE, CHECK_LE, CHECK_LT, CHECK_GE, CHECK_GT。

2. CHECK_NOTNULL(some_ptr) 判断指针非空，通常用于参数检查。

3. 字符串比较，CHECK_STREQ, CHECK_STRNE, CHECK_STRCASEEQ, CHECK_STRCASENE。

4. 浮点数相等判断，CHECK_DOUBLE_EQ, CHECK_NEAR。

5. 还有CHECK_INDEX, CHECK_BOUND判断是否有效的数组下标及上界，但是这两个都要求静态数组，因为是用sizeof计算的。

当这些宏的条件不成立时，glog会LOG(FATAL)打印相关的参数并终止程序，同时这些宏本质上都是输出流，你也可以添加自己的日志消息，像这样：
```python
CHECK_NE(1, 2) << ": the world must be ending!";
```


# static_assert
C++11提供了static_assert，能够实现编译期assert。
```python
static_assert(bool_expression, message);
```

bool_expression为false时会终止编译，并将message打印在编译错误中，注意，message必须为字符串字面量，不能容纳动态信息，这也不难理解，毕竟是一个编译期的动作。

static_assert可以用来做一些环境检查，比如你的代码依赖于32-bit的unsigned int, 可以添加如下的static_assert，

```python
static_assert(sizeof(unsigned int) * CHAR_BIT == 32, "unsigned int is not exactly 32-bit");
```

如果某平台的unsigned int不是32bit，编译会失败从而提醒开发者修改实现。
