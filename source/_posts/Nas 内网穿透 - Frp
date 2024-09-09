title: Nas 内网穿透 - Frp

tags:
  - Nas
  - 内网穿透
  - Frp
categories:
  - 小技巧

---
>[!Success] 使用体验：推荐
> 需要一台公网服务器，速度较快，稳定性尚可，推荐
# frp server
## 安装
[一键安装脚本](https://github.com/wztx/frp-onekey)
```shell
wget https://raw.githubusercontent.com/mvscode/frps-onekey/master/install-frps.sh -O ./install-frps.sh
chmod 700 ./install-frps.sh
./install-frps.sh install
```
## 选项
- bind_post: 5443
- vhost_http_port: 8880
- vhost_https_port: 8881
- dashboard_port: 6443
- dashboard_user: 网页端管理员用户
- dashboard_pwd: 网页端管理员密码
- token: 设置一个Token
- frp subdomain_host: 默认（回车，公网服务器实例 IP ）
- frps max_pool_count: 默认（50）
- log_level: warn
- frps log_max_days: 默认（3 days）
- log_file: 默认
- tcp_mux: 默认
- kcp support: 默认
安装启动完成后显示 frps 信息如下：
You Server IP      : 公网服务器实例 IP
Bind port          : 5443
vhost http port    : 8880
vhost https port   : 8881
Dashboard port     : 6443
Dashboard user     : 网页端管理员用户
Dashboard password : 网页端管理员密码
token              : 自己设置的Token
subdomain_host     : 公网服务器实例 IP
tcp mux            : true
Max Pool count     : 5
Log level          : info
Log max days       : 3
Log file           : enable
transport protocol : enable
kcp bind port      : 5443
quic bind port     : 8882
***
# frp client
## 直接利用 Nas 的 Docker 管理界面拉取镜像
略
***
## 下载导入 Docker Image
- Docker Image：stilleshan/frpc
- 下载为 tar 包：[[Docker Image 网页下载]]
- 下载完成后解压两次得到 .tar 文件，导入Nas即可
> [!Bug] 下载报病毒
> Windows 自带的 MSE 报病毒，需下载至 “排除项”文件夹

## frp client 配置文件 frpc.ini
创建 frpc.ini 并上传至 Nas 的 **共享文件/Docker/frp** 文件夹下。文件内容如下：
```ini
[common]
server_addr = 公网服务器实例 IP
server_port = 5443
token = 自己设置的Token
[webdav]
type = tcp
local_ip = Nas 内网地址
local_port = 5005
remote_port = 8899
```
## 创建并启动 Docker 容器
需更改的配置：
- 存储空间：共享文件/Docker/frp 至 /frp/frp.ini
- 网络：选择 host
- 命令：/frp/frpc -c /frp/frpc.ini （默认为 /frp/frpc -c /frp/frpc.toml）
- 其余默认启动即可
***
# 访问与管理
- 访问：启动完成后访问 ftps所在server:8899 即可
- 管理：http://ftps所在server:6443