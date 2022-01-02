title: VSCode Remote Docker In WSL2 Without Docker Desktop
tags:
  - WSL
  - VSCode
categories:
  - WSL
date: 2022-01-02 18:46:18
---
Windows10中未安装Docker Desktop，而是通过WSL2安装linux内核docker。在使用VSCode Remote运行远程容器时出现问题，无法正常使用。记录解决方案如下：
<!--more-->
# 前提条件
[WSL2 安装 Docker Without Docker desktop](/2022/01/01/WSL2-安装-Docker-Without-Docker-Desktop/)

[设置 WSL 开发环境](https://docs.microsoft.com/zh-cn/windows/wsl/setup/environment#set-up-windows-terminal)

# Windows 不安装Docker Desktop使用Docker命令
## 下载单独的windows docker文件
[windows](https://download.docker.com/win/static/stable/x86_64/)

## 解压并设置环境变量
将解压路径设置至环境变量path中
docker -v 验证成功
docker ps 失败
接下来继续解决

# WSL2 公开 Docker 守护程序并为其创建上下文
在 WSL2 中，更改服务配置以在本地主机上额外公开 Docker 守护程序：
```bash
sudo cp /lib/systemd/system/docker.service /etc/systemd/system/
sudo sed -i 's/\ -H\ fd:\/\//\ -H\ fd:\/\/\ -H\ tcp:\/\/127.0.0.1:2375/g' /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

# Windows 设置DOCKER_HOST
增加环境变量：
DOCKER_HOST = tcp://localhost:2375

验证：
docker ps
docker images
docker run hello-world
均可成功

# Configure VSCode to access to WSL2 docker
To do so, click on the icon (?) on the top right of the section "Containers" and select "Edit settings..."

![VSCode edit docker settings](https://res.cloudinary.com/practicaldev/image/fetch/s--eIDRuGok--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w88fkywtbd5sm4sf1ota.png)

You'll get around 56 settings and you search for _"Docker:Host"_ where you put the line **"tcp://localhost:2375"** where you can replace the highlighted ip address by the one you got before
![VSCode set Docker:Host](https://res.cloudinary.com/practicaldev/image/fetch/s--egPq8goX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h6b02nk14i4xaielwoxr.png)

Once done, you come back to the panel and you click on _"refresh"_ icon (top right of each sections) and you would get information from your **dockerd** running in WSL2
![VSCode it works](https://res.cloudinary.com/practicaldev/image/fetch/s--R86SMWRm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ehfyeoma7e96xao94nqn.png)

# 使用 VS Code 在远程容器中开发
问题解决，参照下文继续[使用 VS Code 在远程容器中开发](https://docs.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-containers#develop-in-remote-containers-using-vs-code)

# 参考资料
[Is it possible to install only the docker cli and not the daemon](https://stackoverflow.com/questions/38675925/is-it-possible-to-install-only-the-docker-cli-and-not-the-daemon)

[Running Windows and Linux containers without Docker Desktop](https://lippertmarkus.com/2021/09/04/containers-without-docker-desktop/)

[How to run docker on Windows without Docker Desktop](https://dev.to/_nicolas_louis_/how-to-run-docker-on-windows-without-docker-desktop-hik)