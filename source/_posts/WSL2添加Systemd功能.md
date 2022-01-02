title: WSL2添加Systemd功能
tags:
  - WSL
categories:
  - WSL
date: 2022-01-02 12:49:59
---
Systemd 是 Linux 系统工具，为系统的启动和管理提供一套完整的解决方案。已成为大多数发行版的标准配置。Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面。根据 Linux 惯例，字母d是守护进程（daemon）的缩写。 Systemd 这个名字的含义，就是它要守护整个系统。WSL2默认是没有systemd服务的，需要自行去安装。
<!--more-->

# 执行命令
[安装脚本](https://github.com/DamionGans/ubuntu-wsl2-systemd-script)

```bash
sudo apt install git
git clone https://github.com/DamionGans/ubuntu-wsl2-systemd-script.git
cd ubuntu-wsl2-systemd-script/
bash ubuntu-wsl2-systemd-script.sh
# Enter your password and wait until the script has finished
```

# 验证安装结果
```
systemctl
```
