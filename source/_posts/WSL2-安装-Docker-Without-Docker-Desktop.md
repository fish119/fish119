title: WSL2 安装 Docker Without Docker Desktop
tags:
  - WSL
categories:
  - WSL
date: 2022-01-01 21:15:30
---
WSL 2 是 WSL 中体系结构的新版本，它更改了 Linux 分发版与 Windows 进行交互的方式。 WSL 2 的主要目标是提高文件系统性能并增加完全的系统调用兼容性。 Windows10 2004正式支持WSL2 ，同时WSL2 也 支持 Windows 10 家庭版。本文记录Windows10 WSL2安装及部署Docker。
<!--more-->
---
# 开启WSL2
## 升级Windows10 至 2004版本
下载[Windows10易升](https://download.microsoft.com/download/8/3/c/83c39dca-2d27-4c24-b98b-0a4d6d921c80/Windows10Upgrade9252.exe)

点击运行，升级至最新版本即可

## 开启虚拟机平台组件

以管理员身份运行PowerShell，输入以下命令回车执行，重启：
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

## 开启Linux子系统组件
以管理员身份运行PowerShell，输入以下命令回车执行，重启：
```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

## 开启Linux子系统组件
下载 [WSL 2 Linux 内核安装包](https://docs.microsoft.com/zh-cn/windows/wsl/wsl2-kernel)

![下载WSL 2 Linux 内核安装包](/uploads/20220101/0201.png)
点击运行即可安装

## 设置Linux子系统使用WSL2
设置所有子系统使用WSL2:（也可设置某一子系统）
```powershell
wsl --set-default-version 2
```
---
# 安装Linux子系统
~~Microsoft Store下载安装，不做记录~~

## 离线安装Linux子系统
下载离线安装包：
[手动下载适用于 Linux 的 Windows 子系统发行版包](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual#download-using-powershell)

![下载适用于 Linux 的 Windows 子系统发行版包](/uploads/20220101/0202.png)

更改安装包后缀名为.zip后解压即可得到ubuntu2004.exe可执行文件

![解压得到Linux子系统]/uploads/20220101/0203.png)

双击执行，即可进入Linux子系统，按提示设置用户名、密码即可使用。

![init linux](/uploads/20220101/0204.png)

完成后升级
```bash
sudo apt update
```

查看Linux子系统信息：
打开Powershell，执行
```powershell
wsl -l -v
```
可见Linux子系统WSL版本为WSL2
![show wsl version](/uploads/20220101/0205.png)

---
# 安装docker
## 更换Linux源
视网络情况及个人喜好选择
```bash
sudo vi /etc/apt/sources.list
```
- 清华
```bash
# 清华大学源
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```
- 中科大
```bash
# 中科大镜像源
deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```
- 阿里
```bash
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

输入以下命令进行换源：
```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak #备份原来的sorce文件
sudo vi /etc/apt/sources.list #编辑源文件
#按dG清空文件原有内容
#按i键进入编辑模式，右键粘贴复制好的源列表
#按ESC，按Shift+:，输入wq回车保存并退出
```

完成后执行更新：
```bash
sudo apt-get update && sudo apt-get upgrade
```

## 安装docker

### 使用第三方脚本安装
如[Rancher的Docker安装脚本](https://github.com/rancher/install-docker)
```bash
#使用rancher提供的docker安装脚本
curl https://releases.rancher.com/install-docker/18.09.sh | sh
```

### 手动安装

1. 安装依赖
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common
```

2. 添加Docker的 GPG 公钥
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

3. 添加软件仓库
```bash
sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
4. 更新源
```bash
sudo apt-get update
```
5. apt-get 安装 Docker
```bash
sudo apt-get install -y docker-ce
```
6. 将当前用户添加到docker用户组
```bash
sudo adduser $USER docker
```

## 验证Docker
**非常重要：重启计算机**
**非常重要：重启计算机**
**非常重要：重启计算机**
开启Docker服务并验证
```bash
sudo service docker start
docker run hello-world
```
![docker setup success](/uploads/20220101/0206.png)

## 更换Docker源
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://5cxjewuf.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

```

## Linux子系统文件位置
运行命令中或资源管理器地址栏输入：
```cmd
\\wsl$
```
即可进入Linux子系统文件目录（其中的mnt子目录本机硬盘目录）
![visit wsl file in windows](/uploads/20220101/0207.png)


# 参考文章
[查看原文](https://blog.csdn.net/qq_41619167/article/details/106170409?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param)

[查看原文](https://www.cnblogs.com/lepeCoder/p/wsl_dir.html)

[非命令方式安装查看原文](https://www.cnblogs.com/shuzhenyu/p/12517385.html)