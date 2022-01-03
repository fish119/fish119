title: WSL2 搭建 Rancher 开发环境
tags:
  - WSL
  - Rancher
categories:
  - - WSL
    - Rancher
date: 2022-01-03 21:10:08
---
记录在WSL2下搭建Rancher开发环境步骤。基本步骤如下：
- 安装并配置WSL2
- 安装 Rancher-2.5.x
- 配置 Rancher
- Helloworld 案例
<!--more-->

# 安装并配置WSL2
- 安装WSL2：略
- 可选：[WSL修改安装目录](/2021/12/25/WSL修改安装目录/)
- [为WSL2分配固定IP](/2021/12/26/为WSL2分配固定IP/)
- [WSL2 安装 Docker Without Docker Desktop](/2022/01/01/WSL2-安装-Docker-Without-Docker-Desktop/)

# 安装 Rancher-2.5.x
直接使用Docker启动Rancher，此处选择2.5.11版本
**注意几点：**
- **避免80、8080、443端口被占用，映射如下：80-8081，443-8443**
- **由于在国内访问，避免因网络导致的各种问题，加入以下参数使用阿里云Rancher镜像：**
```bash
-e CATTLE_AGENT_IMAGE="registry.cn-hangzhou.aliyuncs.com/rancher/rancher-agent:v2.5.11" \
registry.cn-hangzhou.aliyuncs.com/rancher/rancher:v2.5.11
```
- **为了使宿主机（windows）能够直接访问Rancher部署的服务，预先映射部分接口，此处为32700-32799**
```bash
-p 32700-32799:32700-32799 \
```
完整docker run命令如下：
```bash
docker run -d --privileged --restart=unless-stopped \
--name rancher-v2.5.11 \
-p 8081:80 -p 8443:443 \
-p 32700-32799:32700-32799 \
-v /usr/local/rancher/rancher-data:/var/lib/rancher \
-e CATTLE_AGENT_IMAGE="registry.cn-hangzhou.aliyuncs.com/rancher/rancher-agent:v2.5.11" \
registry.cn-hangzhou.aliyuncs.com/rancher/rancher:v2.5.11
```

# 配置 Rancher
上述命令执行完毕后，访问http://wsl2静态IP地址:9443可进入Rancher主界面，设置admin密码后进入Rancher控制台
为方面国内访问，做如下配置：

## 设置默认镜像仓库
从 UI 导航到Settings，然后编辑system-default-registry，Value 设置为registry.cn-hangzhou.aliyuncs.com
![设置默认镜像仓库](https://docs.rancher.cn/assets/images/007S8ZIlly1gek021xwzij31tq0k8gm1-814c8813b9e6c8317079b5b11cfff706.jpg)

## 修改应用商店默认地址
Rancher 默认使用 github 上的 repo 作为应用商店的 URL，如果出现timeout情况，可以将Catalog URL替换成国内的码云地址，步骤如下：
- 导航到全局或项目级别的Apps -> Manage Catalogs
- 单击列表右侧的省略号 -> Edit
- 将Catalog URL替换成码云中的地址即可，Save
- 此时，对应的 Catalog 的状态变为了Refreshed，等待变为Active之后即可正常使用
每个 repo 的对应关系如下：

| 应用商店地址| 应用商店地址 |Gitee 地址|
|  ----  | ----  |----  |
|https://git.rancher.io/helm3-charts|https://github.com/rancher/helm3-charts|https://gitee.com/rancher/helm3-charts| 
|https://git.rancher.io/charts|https://github.com/rancher/charts|https://gitee.com/rancher/charts|
|https://git.rancher.io/system-charts|https://github.com/rancher/charts|https://gitee.com/rancher/system-charts|

[更多设置参见官方文档](https://docs.rancher.cn/docs/rancher2/best-practices/use-in-china/_index/#%E4%BD%BF%E7%94%A8%E5%9B%BD%E5%86%85-rancher-chart-%E5%9C%B0%E5%9D%80%E6%B7%BB%E5%8A%A0-helm-chart-%E4%BB%93%E5%BA%93)

# Helloworld 案例
设置完成后，在default集群下部署rancher/hello-world服务
**在设置端口映射时，80端口如直接随机映射，则宿主机（windows）无法访问，应从上文[安装 Rancher-2.5.x](/2022/01/03/WSL2-搭建-Rancher-开发环境/#安装-Rancher-2-5-x)中Docker run rancher时已预先映射部分接口中选择未使用的端口进行映射
即从32700-32799中选择未使用端口映射，如下图
![部署helloworld](/uploads/20220103/0101.png)

部署完成后，在windows下可直接通过以下地址访问：
http://localhost:映射的端口(如32701)
http://wsl2固定ip:映射的端口(如32701)