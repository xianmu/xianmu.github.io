---
title: authentication models
tags: security
---

认证(Authentication)，指确定一个人的身份。本文主要介绍kerberos，PKI两个经典协议。

# Kerberos

Kerberos基于对称密码系统。

## Setup

Client/Server各自与KGC协商自己的MasterKey，用于与KGC通信时加密。

## Authentication

假定client访问server，server要认证client身份：

1. client请求KGC生成session key;

2. KGC返回两个消息，一个是client MasterKey加密的SessionKey(M1)，另一个是server MasterKey加密的SessionKey及client身份信息(M2);

3. Client获取SessionKey后，发送M2及用SessionKey加密的身份信息，发送给Server;

4. 接受client的访问请求后，server解密M2获得SessionKey和client身份信息，再用SessionKey解密client消息获得client身份信息，比对KGC提供的信息，完成认证。

# PKI

PKI基于公钥密码学。

## Setup

Client/Server各自向CA认证自己获得CA签发的证书，并获得CA自签名的证书。这一步与Kerberos的基本相同。

## Authentication

Client要访问Server，Server认证Client:

1. Client发送自己的证书给Server;

2. Server用CA的公钥验证Client证书有效，发送Client公钥加密的challenge;

3. Client解密challenge发回Server，Server check完成认证。

# Kerberos vs PKI

直观的，PKI中，CA仅完成签发证书，而Kerberos中KGC要参与每次session的建立(SessionKey生成)。

KGC与CA地位不同，KGC存有所有MasterKey，而CA则只有全部的公钥；KGC能解密所有的C/S消息，CA则不行(MITM就是另一回事了)。
