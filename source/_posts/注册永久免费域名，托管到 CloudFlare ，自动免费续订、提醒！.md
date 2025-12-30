title: 注册永久免费域名，托管到 CloudFlare ，自动免费续订、提醒！
tags:
  - windows
categories:
  - 免费域名
  - CloudFlare
date: 2025-12-30 17:23:11
---
非常不错的免费域名注册方案，来自非盈利组织：digitalplat，申请注册后可以直接托管到CloudFlare 平台上进行管理使用。同时使用SubsTracker实现续订提醒。
<!--more-->
# 免费域名申请
- 地址：[digitalplat](https://digitalplat.org/)
- 选择Projects / US.KG NIC，点击 [Visit US.KG Domain Registry](https://nic.us.kg/)
- 点击  "Register a domain"
- 注册，验证，登录。略
- 绑定github，并且start FreDomain仓库可增加一个domain slot
- 左侧菜单栏选择“Domain Registation”，输入二级域名，一级域名选择“.dpdns.org”，点击“check Availability”
- 如未被占用，则注册成功，准备输入Nameserver
# 填写名称服务器Nameserver
- 登录 Cloudflare，选择“域 / 加入域“
- 输入上一步在digitplat获取的.dpdns.org二级域名，如fish119.dpdns.org，其余默认，点击”继续“
- 出现选择计划界面，选择Free
- 复制出现的2个名称服务器，分别粘贴至上一步准备输入Nameserver的 **digitalplat** 页面，保存，提示注册成功
- 回到 Cloudflare 域页面，点击”立即检查名称服务器“，等待邮件提示即可完成
# 自动免费续订、提醒
digitalplat 提供的域名免费政策为：Free if less than 180 days left. Use a key to add 2 years anytime. 因此需要每 180 天续期一次。这里使用 SubsTracker 来进行订阅提醒。
> [!NOTE] Free
> Free if less than 180 days left. Use a key to add 2 years anytime.
## 订阅提醒部署
1. **CloudFlare 创建 KV 空间**
	在Cloudflare 点击左侧：存储和数据库 – KV -创建KV – 用户和键值都填写： SUBSCRIPTIONS_KV
2. **Cloudflare 创建 worker**
	-  登陆Cloudflare，使用”Hello World !”模板创建worker，部署完成后编辑代码
	- 将 [github SubsTracker](https://github.com/wangwangit/SubsTracker) 中的 index.js 代码复制过来，部署，可正常访问 SubsTracker 登录界面
3. **绑定 KV 空间**
	- 进入刚刚创建的 worker 页面，选择 绑定 / 添加绑定，选择”KV命名空间“，点击”添加绑定“
	- ”变量名称“ 和 ”KV 命名空间“ 全部填写 ”SUBSCRIPTIONS_KV“，添加绑定，部署完成
## 订阅提醒设置
1. 访问上面部署设置完成的 Cloudflare workers，登录
	- 用户名：admin
	- 密码：password （*登录后修改密码*）
2. 系统配置
	- 修改密码
	- 通知方式选择 NotifyX，API Key从 [NotifyX平台](https://www.notifyx.cn/) 获取
	- 测试 NotifyX 通知，成功后保存配置即可
3. 设置提醒
	- 订阅列表 / 添加新订阅
	- 周期 6 个月(180天)，提醒提前量 3 天
	- 其余自行按需设置，保存即可
## 订阅提醒服务绑定域名（可选）
- 回到 Cloudflare works 页面，点击”设置“
- 在自定义域中添加上面注册的免费域名，可以再增加前缀设置到3级域名
完成