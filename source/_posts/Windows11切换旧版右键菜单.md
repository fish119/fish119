title: Windows11切换旧版右键菜单
tags:
  - windows
categories:
  - 小技巧
date: 2022-02-01 10:33:18
---
升级尝鲜Windows11，第一感觉就是右键菜单极不习惯，Google了如何切换到Windows10菜单，方法如下：
<!--more-->
# 切换到旧版右键菜单：
```bash
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```
# 恢复回Win11右键菜单：
```bash
reg delete "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
```
# 重启Windows资源管理器生效：
```bash
taskkill /f /im explorer.exe & start explorer.exe
```