---
title: iterm2 保存ssh密码
abbrlink: 27587
date: 2017-10-24 15:14:01
tags:
categories:
- mac
---
使用脚本连接
1.创建脚本文件
```bash
   vim ~/.ssh/ssh1
```
<!--more-->
2.粘贴内容
```bash
  #!/usr/bin/expect -f
  set user <用户名>
  set host <ip地址>
  set password <密码>
  set timeout -1
  spawn ssh $user@$host
  expect "*assword:*"
  send "$password\r"
  interact
  expect eof
```
3.打开iterm2->Profiles

新建连接

commod 处执行 expect ~/.ssh/ssh1

>执行连接是先手动连接一次ssh 保存seesion key
