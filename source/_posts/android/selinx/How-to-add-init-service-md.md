---
title: init: Service xxx does not have a SELinux domain defined.
date: 2017-07-20 15:08:51
categories: android
tags: [SELinux] [android] [original]
---

假如需要一个Service xxx.

1. 增加xxx.te
```
    type xxx, domain;
    type xxx_exec, exec_type, file_type;
    init_daemon_domain(xxx)
```
2. add xxx_exec into file_contexts
```
    /system/xbin/xxx u:object_r:xxx_exec:s0
```
3. 重新编译bootimage和xxx本身
    rebuild xxx是必须的。原因在于，selinux的规则似乎会和elf文件绑定？？
