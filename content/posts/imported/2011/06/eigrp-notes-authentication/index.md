---
title: "EIGRP Notes - Authentication"
date: 2011-06-10
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "authentication"
  - "ccie"
  - "chain"
  - "eigrp"
  - "key"
  - "md5"
  - "written"
---

Corrections - I invite them.

1\.  Create the keys in the keychain.

> ```
> R101(config)#key chain KEYCHAIN
> R101(config-keychain)#key 1
> R101(config-keychain-key)#key-str
> R101(config-keychain-key)#key-string MYKEY
> ```

2\.  Enable authentication on an interface.

> ```
> R101(config-if)#ip authentication mode eigrp 1 md5
> ```

3\.  Associate keychain with EIGRP.

> ```
> ip authentication key-chain eigrp 1 KEYCHAIN
> ```
