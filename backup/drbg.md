---
title: deterministic random bit generator
tags: cryptography
---

CSPRNG/DRBG是非常重要而且基础的密码学原语，很多加密系统依赖于安全的伪随机数生成，如RSA中就要用到DRBG来生成随机大素数。本文介绍SP-800-90A标准化的几种DRBG。
