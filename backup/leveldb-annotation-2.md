---
title: leveldb-annotation-2
tags: database
---

接下来先看一些通用基础类。

# Slice 

Slice是std::string/char array的view。Slice本身只是保存了字符指针及长度，因其没有拷贝底层字符串，所以需要确保其生命期内窗口中的底层字符串不会释放。也不能通过Slice修改底层字符串。Slice比较简单，这里省略源码。

# Status

Status是error_code, error_message的一个封装，Status在内部将错误码和信息按下面格式编码在一段连续的内存中：

```
+-------------------------------+
|length(4B)|code(1B)|  message  |
+-------------------------------+
```

注意，这里length表示的仅仅是message的长度，不包含code这1B，也不包含自己的4B。message是调用方传入的1~2个错误信息，Status自动添加": "拼接它们。在ToString方法中，Status前缀错误名拼接message返回。代码较简单，省略。

# Histogram

Histogram为benchmark提供了一些统计信息，包括中位数，均值，标准差，分位数等等。其中分位值是估计值，中位数直接调用分位值接口，所以也是估计值，均值及标准差是精确值。Histogram比较简单，与leveldb主要逻辑也没有太大关系，省略代码。
