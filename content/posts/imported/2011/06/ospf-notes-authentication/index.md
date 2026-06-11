---
title: "OSPF Notes - Authentication"
date: 2011-06-10
categories: 
  - "ccie"
  - "cisco"
tags: 
  - "350-001"
  - "authentication"
  - "ccie"
  - "digest"
  - "md5"
  - "ospf"
  - "type"
  - "written"
---

Corrections appreciated.

**Type 0** : No authentication.  This is the default type.

> ```
> R0(config-if)#ip ospf authentication null
> -----
> R0(config-router)#area 1 virtual-link 2.2.2.2 authentication null 
> 
> 
> ```

**Type 1** : Clear text authentication

> ```
> -----
> R0(config-if)#ip ospf authentication
>   - or -
> R0(config-router)#area 1 authentication
> R0(config-if)#ip ospf authentication-key MYKEY live sex online
> -----
> R0(config-router)#area 1 virtual-link 2.2.2.2 authentication-key MYKEY
> ```

**Type 2** : MD5 authentication

> ```
> -----
> R0(config-if)#ip ospf authentication message-digest
>   - or -
> R0(config-router)#area 1 authentication message-digest
> R0(config-if)#ip ospf message-digest-key 1 md5 MYKEY
> -----
> R0(config-router)#area 1 virtual-link 2.2.2.2 authentication message-digest message-digest-key 1 md5 MYKEY
> ```
