title: 网页下载Docker Image

tags:
  - Docker
categories:
  - 小技巧
date: 2024-09-03 17:11:18
---
近期国内Docker镜像及加速无法使用，记录Docker Image 网页下载方案
<!--more-->

## Step1 Fork Repository
仓库地址：[https://github.com/wukongdaily/DockerTarBuilder](https://github.com/wukongdaily/DockerTarBuilder)
点击Fork，所有选项默认即可
***
## Step2 运行Github Actions
- 点击Actions标签
- 选择相应架构的workflow（如：x86-64 Pull and Save Docker Image）
- 点击“Run Workflow”，输入 Image 名称如 nginx、alpine:latest后，再次点击“Run Workflow"
- 等待片刻，可以看到Workflow运行完成，点击该Workflow
- 在”Artifacts“列表中出现已经打包好的tar文件，下载即可