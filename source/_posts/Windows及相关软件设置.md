title: Windows及相关软件设置
tags:
  - windows
categories:
  - 小技巧
date: 2024-09-01 12:11:18
---
## Windows 11 传统右键菜单
cmd 以管理员身份运行下列代码，完成后重启
```reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```
## Office Tools Plus激活
### Office, Visio, Project 2016
```ospp /inslicid ProPlusVolume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid VisioProXVolume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid ProjectProXVolume /sethst:kms.loli.best /setprt:1688 /act
```
### Office, Visio, Project 2019
```ospp /inslicid ProPlus2019Volume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid VisioPro2019Volume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid ProjectPro2019Volume /sethst:kms.loli.best /setprt:1688 /act
```
### Office LTSC 2021, Visio LTSC 2021, Project 2021
```ospp /inslicid ProPlus2019Volume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid VisioPro2019Volume /sethst:kms.loli.best /setprt:1688 /act

ospp /inslicid ProjectPro2019Volume /sethst:kms.loli.best /setprt:1688 /act
```
## 添加Sublimtext到右键菜单
新建“添加sublimtext到右键菜单.reg”文件后运行
```Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\*\shell\SublimeText3]

@="Open With SublimeText3"

"Icon"="F:\\Soft\\Sublime Text Build 3143 x64\\sublime_text.exe,0"

[HKEY_CLASSES_ROOT\*\shell\SublimeText3\command]

@="F:\\Soft\\Sublime Text Build 3143 x64\\sublime_text.exe %1"

[HKEY_CLASSES_ROOT\Directory\shell\SublimeText3]

@="Open With SublimeText3"

"Icon"="F:\\Soft\\Sublime Text Build 3143 x64\\sublime_text.exe,0"

[HKEY_CLASSES_ROOT\Directory\shell\SublimeText3\command]

@="F:\\Soft\\Sublime Text Build 3143 x64\\sublime_text.exe %1"
```
## Python(PIP) 更换阿里云源
```pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
```
## PhotoShop 文件拖拽打开
编辑注册表：
```HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```
找到“EableLUA”文件，并双击将此文件打开，把值改为0，点击确认，需要重启电脑
## Regjump
以管理员身份运行wt或cmd，执行：
``` copy F:\Soft\RegJump\regjump.exe C:\Windows\System32
```
