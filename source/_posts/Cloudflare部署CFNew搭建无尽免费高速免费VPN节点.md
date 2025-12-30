title: Cloudflare部署CFNew搭建无尽免费高速免费VPN节点
tags:
  - CloudFlare
  - VPN
  - v2ray
categories:
  - 免费域名
date: 2025-12-30 21:32:00
---
在CloudFlare上利用CF workers 搭建海量的代理节点！免费！速度还不错！优选IP地址后速度极快！可以秒开4K、8K视频。
<!--more-->
1. CloudFlare 创建 Wokers KV，名称自拟

2. CloudFlare 以 “Hello World !” 模板创建Worker

3. Worker 绑定 Wokers KV
	**变量名称必须为大写的 "C" **

4. 绑定域名
	**屏蔽 workers.dev 以及 预览URL**

5. Worker 添加变量
- 设置 / 变量和机密 / 添加
- u = 用 v2rayN 生成uuid

6. 部署 cfnew
- github地址：[cfnew](https://github.com/byJoey/cfnew)
- 复制 "少年你相信光吗" 代码至Worker代码，保存，部署

7. 配置 cfnew
- 访问 worker 的自定义域名，按提示输入上面生成过的uuid，也就是Worker的变量u的值
- 设置
	- 指定区域
	- 自定义Proxy IP（可选）
	- **启用优选域名：出现 “https://cloudflare-dns.com/dns-query: EOF” 错误时，取消选中此项可部分解决**

8. 配置v2ray
- 获取v2ray地址
- v2rayN添加订阅分组，自动更新间隔60分钟

完成

参考：
[cfnew git hub](https://github.com/byJoey/cfnew)
[图文教程](https://www.freedidi.com/21262.html)
[视频教程](https://www.youtube.com/watch?v=5XrayNHLWrg)