title: 为WSL2分配固定IP
tags:
  - WSL
categories:
  - WSL
date: 2021-12-26 11:03:08
---
问题：某些服务需要固定IP来访，而wsl2每次重启都会分配一个随机的 ip
解决方案：
 - 宿主机（Windows10）为WSL2虚拟交换机设置固定IP
 - WSL2下编写脚本，设置固定IP并绑定至虚拟交换机
 - 脚本随WSL2启动
 
<!--more-->

# 宿主机（Windows10）创建虚拟交换机并设置固定IP
命令方式：
```bash
netsh interface ip add address "vEthernet (WSL)" 192.168.119.119 255.255.255.0
```
GUI方式：
- 打开“控制面板\网络和 Internet\网络连接”
- 找到“vEthernet (WSL)”虚拟网卡，右键，属性
- 设置IP为：192.168.119.119，子网掩码为：255.255.255.0

# WSL2下编写脚本，设置固定IP
```bash
if ip addr | grep "192.168.119.2"
then
	echo "\nIP already exsit"
else
	ip addr add 192.168.119.2/24 broadcast 192.168.199.255 dev eth0 label eth0:1
	echo "\nSet Static IP Success"
fi
```

# WSL2下设置开机启动
***此处未完成***
使用/etc/init.d、/etc/systemd/system下创建service等方式均未成功
暂时将脚本加至 /etc/init.d/docker文件start docker之前
确保在docker启动前已设置完静态IP，则docker启动时可绑定至该IP
```bash
………………
# Having non-zero limits causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
if [ "$BASH" ]; then
        ulimit -u unlimited
else
        ulimit -p unlimited
fi
# 此处加入设置IP脚本
if ip addr | grep "192.168.119.2"
then
        echo "\nIP already exsit"
else
        ip addr add 192.168.119.2/24 broadcast 192.168.199.255 dev eth0 label eth0:1
        echo "\nSet Static IP Success"
fi
#设置IP脚本结束
log_begin_msg "Starting $DOCKER_DESC: $BASE"
start-stop-daemon
```