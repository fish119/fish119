title: ClawCloud 免费部署 3X-ui
tags:
  - ClawCloud
  - VPN
  - v2ray
categories:
  - VPN
date: 2026-01-05 14:52:00
---
Claw Cloud Run 从 4 月 1 日开始提供永久免费的云服务部署额度。目前，只要你的 [GitHub](https://zhida.zhihu.com/search?content_id=256827190&content_type=Article&match_order=1&q=GitHub&zhida_source=entity) 账号注册超过 180 天，通过 GitHub 注册即可享受每月 5 美元的免费额度，无需任何信用卡或其他认证。
你可以使用这些免费额度来部署各种服务和应用。应用市场提供了大量开源应用，支持一键部署。这里我们使用ClawCloud免费额度部署X-ui搭建梯子。
<!--more-->
# 注册登录 ClawCloud
- [ClawCloude官网](https://console.run.claw.cloud/signin?link=e6THD5hjrN)
- 使用 GitHub 账号登录即可
# ClawCloud 创建 APP
- 在Dashboard 中点击 "App Launchpad"，然后点击右上角 ”Create APP“
- Image 输入：  metaligh/3x-ui
- NetWork：添加3个端口，**都需要打开 “Public Access”**。其中2053端口为3X-ui使用，其他两个端口随意，作为梯子入站端口。例如：
	- 2053 端口：3x-ui所需端口
	- 80：ws协议使用端口
	- 2054：xhttp协议使用端口
- Local Storage：增加两个本地挂载目录：
	- /etc/x-ui/
	- /etc/letsencrypt/
- **演示其中一个入站节点使用 Claudflare 自定义域名（可选）**
	- NetWork 处 2054端口，点击端口后面的 “Custom Domain”
	- 复制原有的 CNAME，我们成为 ClawCloude CNAME
	- 登录Cloudflare，选择自己可用的域名,如yourdomain.com，点击“DNS / 记录”，增加一条CNAME记录
	- 名称按需输入，如XCC，目标输入ClawCloude CNAME，**代理状态的小云朵点击取消掉**使之改为“仅 DNS”
	- 点击保存，复制刚才创建的CNAME记录的子域名，如：XCC.yourdomain.com
	- 回到刚才 ClawCloud Custom Domain 界面，粘贴自定义域名，点击 Confirm 即可
- 点击 **Deploy Application** 部署应用
- 部署完成后，可在Network部分看到3个端口的 “Public Address”
# 3X-ui 配置
## 修改登录密码（切记）
- **Deploy Application** 完成后，在界面的 “Network” 部分，点击2053端口后链接，进入3X-Ui面板
- 默认用户名、密码均为：admin
- **设置用户名密码**：“面板设置 / 安全设定” ，设置用户名、密码
## 创建节点
- 3X-ui 面板中点击”入站列表 / 添加入站“
- 添加两条记录：
	- ws协议：
		- 协议：vless
		- 端口：上面创建过的两个端口中选择一个，本文为80
		- 传输：WebSocket
		- 其余默认，点保存即可
	- Xhttp协议：
		- 协议：vless
		- 端口：上面创建过的两个端口中选择一个，本文为2054
		- 传输：splithttp
		- 其余默认，点保存即可
- 完成后，选择相应记录的”菜单 / 导出链接”
# v2rayN 配置
- 从剪贴板导入分享链接
- 修改导入的链接：
	- 地址：改为对应端口在ClawCloud / Network部分的 “Public Address”，去掉"https://” 例如xhttp协议的地址为XCC.yourdomain.com
	- 端口：改为443
	- 传输层安全（TLS）：tls
	- 跳过证书验证（allowlnsecure）：False
完成