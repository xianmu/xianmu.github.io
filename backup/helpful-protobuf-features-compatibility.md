---
title: helpful protobuf features
tags: development
---

从设计上来说，Protobuf及json都是前后兼容的，向前兼容大家并不陌生，但有些人对向后兼容有一些诧异，其实这里前后兼容指新协议能够反序列化旧协议序列化的消息，旧协议也能够反序列化新协议序列化的消息。
