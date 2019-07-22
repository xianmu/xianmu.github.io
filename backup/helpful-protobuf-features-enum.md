---
title: helpful protobuf features-enum
tags: development
---

Protobuf的枚举(enum)类型不同于C/C++的枚举类型，其带有范围检查。

# reflection

众所周知，protobuf本身是支持反射的，这在使用时可以带来很多便利。十分建议使用protobuf的enum来定义return code，可以通过反射获取其名称而不需要手动填充一个lookup table建立code-message映射。特别是在日志打印或者其他涉及与人交互的地方，可以极大的提高可读性；否则，还需要人工反查return code的含义。例如：


# compatability

Protobuf的enum因带有范围检查，对于不在范围内的值，会被映射成第一个枚举值，这会导致一定的兼容性问题。例如：

使用protobuf的enum，通常将第一个枚举值留出来，命名为Unknown或者Unexpected，以规避上述问题，代码中一样要小心处理该值。
