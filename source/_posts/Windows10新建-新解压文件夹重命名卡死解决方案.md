title: Windows10新建/新解压文件夹重命名卡死解决方案
tags:
  - windows
categories:
  - 小技巧
date: 2022-01-05 09:34:03
---
最近经常出现Windows10新建文件夹或从压缩文件中解压出文件夹之后，重命名卡死，重启explorer问题依然存在。经google后发现是windows search服务的问题。
<!--more-->
- 打开windows服务
- 选择windows search服务
- 属性/手动或禁用
- 停止服务
- 问题解决。