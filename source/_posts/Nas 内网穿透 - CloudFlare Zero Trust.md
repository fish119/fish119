title: Nas 内网穿透 - CloudFlare Zero Trust

tags:
  - Nas
  - 内网穿透
  - Cloudflare
categories:
  - 小技巧
---
记录绿联 Nas 使用 Frp 进行内网穿透的过程
<!--more-->
>[!warning] 使用体验
> 稳定但速度较慢
# 前置条件
一个域名，并已经托管至 Cloudflare 具体见：
[US.KG 获取一个永久免费二级域名并托管到 Cloudflare](https://register.us.kg)
***
# 开启Zero Trust
1. 在 [Cloudflare Dashboard](https://dash.cloudflare.com/) 中选择 "Zero Trust"
2. 在新页面中选择 Plan 为 Free
3. 添加付款方式，不会扣款，免费
4. 等待完成即可
***
# 配置 Zero Trust
1. 在 Zero Trust > Access > Tunnels 中创建 Tunnel
2. tunnel type 选择 "Cloudflared" ，Next
3. 输入 Tunnel name , Save
4. 下载客户端，我们将在 Nas 中使用 Docker 部署，因此选择 Docker
5. 记录下  [[Nas 内网穿透信息#Cloudflare Zero Trust Token|Token]] , Next
6. Domain 选择 [[US.KG 获取一个永久免费二级域名并托管到 Cloudflare]] 中注册的域名，其余按需设置 Subdomain
7. Save hostname 即可
# Nas 端安装配置
因尚未安装客户端，此时 Tunnels 的 Status 为 Down，下面将在 Nas 中以 Docker 方式安装 Tunnels 客户端
1. [[Docker Image 网页下载]] ，并上传至Nas
2. Nas / Docker / 镜像 / 本地镜像 / 添加本地镜像 / 从NAS导入 即可
3. SSH 连接至 Nas，运行以下命令
```bash
docker run cloudflare/cloudflared:latest tunnel --no-autoupdate run --token eyJhIjoiYTQ0MzViZmNiM2M5MzViNzE4OTM5MzA2NDZhOTlmYzAiLCJ0IjoiNTgxYzRiOGUtOTgxYy00Y2JhLWE0YWItZDdkMTBiZWMyOGVhIiwicyI6Ik5UZ3dOR00zTldRdE9ESXpaUzAwTWpBMExUZ3paV1V0WW1VMFkyTTJZV0V3TXpFeiJ9
```
4. 或 Nas / 容器 / 创建容器 / 选择已导入的镜像 / 确定 / 配置参数 --token即可
# 验证与 Cloudflare 维护
- 安装完成后返回 Cloudflare , 看到 Tunnels 的 Status 为 Healthy
- 点击 Tunnel name 最右侧的更过按钮 / configure / Public Hostname 可以进行配置
- 点击 Tunnel name ， 可以访问已配置的内网服务即代表完成