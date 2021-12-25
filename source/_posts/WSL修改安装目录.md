title: WSL修改安装目录
tags:
  - WSL
  - windows
categories:
  - WSL
date: 2021-12-25 13:00:04
---
WSL默认安装在C盘，为了避免占用日益紧张的C盘空间，需要修改其安装目录至其他分区。
<!--more-->

### 首先查看所有分发版本

```powershell
wsl -l --all  -v
```

>  NAME STATE VERSION  
>  \* Ubuntu-20.04 Running 2

### 导出分发版为tar文件到d盘

```powershell
wsl --export Ubuntu-20.04 d:\ubuntu20.04.tar
```

### 注销当前分发版

```powershell
wsl --unregister Ubuntu-20.04
```

### 重新导入并安装分发版在d:\\ubuntu

```powershell
wsl --import Ubuntu-20.04 d:\ubuntu d:\ubuntu20.04.tar --version 2
```

### 设置默认登陆用户为安装时用户名

```powershell
ubuntu2004 config --default-user Username
```

### 删除tar文件(可选)

```powershell
del d:\ubuntu20.04.tar
```