title: WSL2添加Systemd功能后VSCode Remote无法打开
tags:
  - WSL
categories:
  - WSL
date: 2022-01-02 12:54:33
---
在上一篇[《WSL2添加Systemd功能》](/2022/01/02/WSL2添加Systemd功能/)中成功为WLS2添加了Systemd功能，并成功在[WLS2安装Minikube](/2021/12/26/WSL2通过Minikube使用Kubernetes/)后，发现在wsl2 bash下使用code .命令无法打开已安装Remote插件的VSCode（开启Systemd前可正常使用）。记录解决方案如下：
<!--more-->
# 尝试绝对路径打开VSCode
在wsl bash下,进入VSCode安装路径，查看是否可正常打开VSCode
```bash
/mnt/c/Program\ Files/Microsoft\ VS\ Code/bin/code .
```
如可以正常打开，则使用alias为指定Code命令别名即可

# 使用alias为指定Code命令别名
临时指定
```bash
alias code='/mnt/c/Program\ Files/Microsoft\ VS\ Code/bin/code'
```
永久指定
```bash
cd   /home/用户名
sudo vim ./.bashrc
```
在如下位置加入alias
```bash
……
# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias code='/mnt/c/Program\ Files/Microsoft\ VS\ Code/bin/code'
```
保存后执行
```bash
source ~/.bashrc
```

# 如绝对路径仍无法打开VScode
- Stop WSL (wsl --shutdown)
- Remove WSL Remote extension from VSCode
- Reinstall WSL Remote extension from VSCode