---
title: helpful protobuf features
tags: development
---

Protobuf的schemaless是其与json这样的序列化格式核心的区别。说schemaless是带有误导性的，更准确的讲，protobuf的schema和数据是分离的，即序列化之后的数据不带有schema信息；json与之相对，序列化后的数据中包含schema，所以json在序列化反序列化的时候还要处理schema，自然比protobuf慢一些。
