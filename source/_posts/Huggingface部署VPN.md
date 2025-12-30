title: Huggingface 部署VPN

tags:
	- VPN
	- v2ray
	- Cloudflare
	- Huggingface 
categories:
	- VPN 
date: 2025-12-30 14:05:59

---
使用Huggingface 部署VPN，使用CloudFlare反代域名套 CDN，免费
<!--more-->
> [!warning] 使用体验 稳定、免费，但速度较慢
# HuggingFace部署
## 创建HuggingFace空间
1. 注册或登录后，点击右上角头像，选择“New Space”
2. 参数设置，点击“Create Space”：
	- space name、Short description：随意填写
	- Select the Space SDK：Docker
	- Space hardware：CPU Basic
	- 最后的模式选择：Public
## 通过Github创建Docker Image
### Fork Github 项目
1. 打开下面的github项目地址：[基于serverless实现的vless+trojan双协议代理](https://github.com/eooce/node-ws)
2. Fork 该项目，修改Repository Name（可不修改），**取消勾选“Copy the main branch only”（必须）**，点击Create Fork
### 修改代码
#### main分支：
workflows/build-hug-image.yml：“workflows”文件夹下的“build-hug-image.yml”文件
44行：构建镜像名称，建议修改
```yml
tags: |
	ghcr.io/${{ github.repository_owner }}/XXXXX:latest
```

#### hug分支
1. 修改代码：
	main分支修改完成后，修改hug分支下的“index.js“文件
	 - 第11行UUID：通过v2rayN或其他工具生成一个即可
	 - 第15行域名：暂时填写HuggingFace为空间分配的默认域名，格式为：用户名-空间名.hf.space，无需http前缀。如：username-spacename.hf.space
	 - 第18行订阅路径：默认为sub，可不修改。VPN订阅地址为：上一步的域名/订阅路径。如：username-spacename.hf.space/sub
2. 混淆代码：
	复制修改完的index.js全部代码
	打开[https://nav.eooce.com/](https://nav.eooce.com/)，选择Tools/JS混淆，选择中等强度混淆即可
3. 将混淆后的代码粘贴回github，commit即可
### Build Docker Image
- 点击Github项目中的Actions，运行其中的”Build huggingface Image“ workflow，等待运行完成
- 回到Github项目主页，找到右下角packges,查看构建好的镜像，记录镜像地址
## Hugging Face Add Docker File
- 回到Hugging Face创建的空间页面，在空间的菜单栏选择“Files”
- 点击文件列表右上角的“Contribute"，选择”Create a new file“
- 文件名必须为：Dockerfile。文件内容为：From 上面记录的镜像地址；ENV Domain = HuggingFace为空间分配的默认域名
```DockerFile
FROM ghcr.io/XXXX/XXXXX:latest
ENV DOMAIN=username-spacename.hf.space
```
- 填写好Commit Message后，点击”Commit new file to main“，开始自动building
- 此时返回APP标签，点击文件列表右上角三个点菜单，选择”Embed this Space“，可以看到 HuggingFace为空间分配的默认域名
- 等待building完成之后，APP页面可以看到伪装的页面
## 获取地址及使用
订阅地址： HuggingFace为空间分配的默认域名/订阅路径。此处为：username-spacename.hf.space/sub
v2rayN中直接添加该订阅即可。也可浏览器访问该地址，复制结果，在v2rayN中导入。
***
# 利用Cloudflare 反代域名套 CDN（可选）
### Cloudflare反代
- 登录Cloudflare，选择”计算和AI / Workers 和 Pages“
- 选择”创建应用程序 / 从 Hello World! 开始“
- 输入名称，部署
- 完成后编辑代码，返回github仓库，找到”使用cloudflare workers 或 snippets 反代域名给节点套cdn加速“处的代码，复制到编辑框修改如下：
```javascript
export default {
  async fetch(request, env) {
      let url = new URL(request.url);
      if (url.pathname.startsWith('/')) {
          var arrStr = [
              'username-spacename.hf.space', // 此处单引号里填写你的节点伪装域名
          ];
          url.protocol = 'https:'
          url.hostname = getRandomArray(arrStr)
          let new_request = new Request(url, request);
          return fetch(new_request);
      }
      return env.ASSETS.fetch(request);
  },
};
function getRandomArray(array) {
    const randomIndex = Math.floor(Math.random() * array.length);
    return array[randomIndex];
}
```
- 编辑完成后点击”部署“即可
### 添加自定义域名
- 在上面部署完成的Cloudflare Workers中选择”设置“
- 在”域和路由“中选择”添加 / 自定义域“
- 选取托管在Cloudflare中可用的域名，输入自定义的二级域名即可
- 点击”添加域“，完成
### 根据反代更改DockerFile文件
返回Hugging Face，修改前面创建的dockerfile文件，将ENV DOMAIN修改为上面的二级域名即可：
```DockerFile
ENV DOMAIN=自定义的二级域名
```
重新获取订阅地址，更新v2rayN订阅或重新导入即可。

参考：
[Huggingface 部署永久免费高速VPN节点，无限流量，解锁ChatGPT, 奈飞，油管等，无需保活](https://www.nodeloc.com/t/topic/67074/3)
[Youtube视频](https://www.youtube.com/watch?v=XERxg9AODeo)