title: Nas 内网穿透 - Cpolar

tags:
  - Nas
  - 内网穿透
  - Cpolar
categories:
  - 小技巧
date: 2024-09-03 17:11:18

---
记录绿联 Nas 使用 Cpolar 进行内网穿透的过程
<!--more-->

## 注册 Cpolar 并获得 Authtoken
[https://www.cpolar.com/](https://www.cpolar.com/)
注册成功后无需选择套餐，直接点击“仪表盘”
点击右侧”验证“按钮，即可获得 Authtoken
## 下载 Cpolar Docker 镜像
因Docker Hub及国内镜像镜像均无法使用，通过 [Docker Image 网页下载](https://fish119.vercel.app/2024/09/03/37254cb8d53d/) 方案下载镜像为Tar
镜像名称：probezy/cpolar
## Docker 安装运行镜像
[Cpolar官方文档](https://www.cpolar.com/blog/docker-container-installation-cpolar)
1. 将下载的镜像上传至Nas 
2. 镜像 / 本地镜像 / 添加镜像 / 从Nas导入
3. 容器 / 创建容器 / 选择镜像 / 确定
	- 网络模式选择 Host
	- 取消”创建后运行“选项，因缺少文件无法运行，运行将出现如下错误
```Docker
Reading configuration file /usr/local/etc/cpolar/cpolar.yml
Failed to read configuration file /usr/local/etc/cpolar/cpolar.yml: open /usr/loca
```

## Nas 运行Cpolar Docker 客户端
1. 点击上文创建的容器，在”基本信息“页面下的”存储空间“可找到该文件应存储于Nas的路径
2. 根据上文提示信息，新建空白的 cpolar.yml 文件，上传至相应路径
3. 再次点击容器的”运行“按钮，容器成功运行
## Nas Cpolar Docker 客户端 AuthTocken验证
1. 点击容器的”终端“，新增 /bin/bash ，新增完成后点击进入
2. 运行下列命令，将 [[#注册并获得Authtoken]] 进行验证
```shell
cpolar authtoken 复制的token
```

3. 出现下列提示
```yaml
Authtoken saved to configuration file: /usr/local/etc/cpolar/cpolar.yml
```

4. 验证，仍在 docker bash 环境下，运行下列命令
```bash
cpolar http 8081
```

出现如下图所示结果即代表配置成功，Ctrl+C 退出docker bash即可
<image src="https://images.cpolar.com/img/-Synology20230117153107.png" width=600px/>
## 增加Cpolar隧道
输入http://nas局域网ip:9200


输入 Cpolar 的用户名、密码登录
在 ”隧道管理 / 创建隧道” 中创建隧道，内容包括：
- 隧道名称
- 指定要穿透的端口
- 选择 "随机域名"
- 选择地区 启动即可
在 “状态 / 在线隧道列表 ” 可见已完成穿透的隧道，通过其中的 "公网地址" 即可访问