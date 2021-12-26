title: WSL2无法访问网络
tags:
  - WSL
categories:
  - WSL
date: 2021-12-26 13:42:49
---
WSL2莫名其妙的连不上网，ping 域名不通，ping ip 不通，判断并非dns或hostname问题，而是网络问题。

关闭WSL后重新设置Windows宿主机网络，问题解决。命令如下：
<!--more-->
run powershell as administrator
```bash
wsl --shutdown
netsh winsock reset
netsh int ip reset all
netsh winhttp reset proxy
ipconfig /flushdns
```
重启电脑后问题解决。