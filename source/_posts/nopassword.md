---
title: mac/ubuntu/linux 配置sudo免密码
date: 2021-03-22 12:00:00
categories: 'Diary'
tags: 'Mac'
---

## mac/ubuntu/linux 配置sudo免密码

``` shell

    1 打开命令窗口输入如下命令：
    sudo visudo 或者  sudo vi /etc/sudoers
    2 替换 #%admin ALL=(ALL) ALL 为
    %admin ALL=(ALL) NOPASSWD: NOPASSWD: ALL

```
