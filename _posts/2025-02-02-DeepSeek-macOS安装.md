---
layout:     post
title:      MacOS安装国产DeepSeek
subtitle:   国产DeepSeekMacOS安装
date:       2025-02-02
author:     hdj
header-img: img/bgs/girl-3.jpg
catalog: true
categories : [AI, DeepSeek]

tags:
    - AI
    - DeepSeek
---

DeepSeek挺火，凑个热闹(用的是MAC m1)，苹果笔记本本地部署*deepseek*主要用到*Ollama*与open-webui

最终在本地运行浏览器实现的效果如下：
![say_hello_comond](http://hdj2048228.github.io/img/2025-02/brower_hello.png)


## 1. 安装Ollama
   “Ollama” 是一个轻量级的 AI 模型运行时环境（runtime），旨在简化在本地部署和使用大语言模型（LLM）的过程。它由 Vicarious 公司开发，支持多种 AI 模型（如 GPT-3、GPT-4 等），并允许用户通过简单的命令行或 API 接口与这些模型进行交互。

网页搜索[Ollama](https://ollama.com/)


点击Download
![dowload](http://hdj2048228.github.io/img/2025-02/deepseek_download.png)


选择MacOS，点击download for MacOS （使用科学上网下载更快）

![dowload](http://hdj2048228.github.io/img/2025-02/deepseek_macOS.png)


下载的文件名为Ollama-darwin.zip，解压后得到一个羊驼图标的Ollama，正常安装（安装时选择move to Applications可以在启动台中找到）， 安装完可以看到一个小羊驼图标。

![dowload](http://hdj2048228.github.io/img/2025-02/ollama.png)

## 2. 安装大模型
   在刚才的Ollama界面左上角，选择模型，可以看到许多大模型，我选择的是deepseek-r1


选择参数版本，如果电脑配置一般可以选择1.5b，好的话可以选择32b或者更高都可以。
选择的参数版本越高运行的会更慢，但给出的答案相对好一些。我的电脑为M3 Max芯片，下载了一个7b和32b的，7b的速度很快，32b思考的速度要慢一些，并且使用32b时GPU是在全速运行的

以7b为例，点击右侧的复制按钮， 复制代码ollama run deepseek-r1

![dowload](http://hdj2048228.github.io/img/2025-02/deepseek_r1.png)


打开本地的命令行，用管理员权限进行下载，即sudo ollama run deepseek-r1，输入密码进行下载（下载速度稍慢）

![dowload](http://hdj2048228.github.io/img/2025-02/dp_install.png)

下载完成就可以进行对话了，但这样很不方便，接下来我们可以安装open-webui建立像ChatGPT一样的对话框

## 3. 安装docker

   网页搜索[docker](https://www.docker.com/)


根据电脑芯片选择下载版本，我选择的是​Download for Mac - Apple Silicon

![dowload](http://hdj2048228.github.io/img/2025-02/docker.png)


下载之后一键安装
## 4. 安装运行open-webui
   进入Github官网，可以从刚刚Ollama左上角第二个选项Github进入（点击小黑猫进入首页），也可以直接搜索进入首页

 [点击进入open-webui](https://github.com/open-webui/open-webui)


往下拉，找到How to Install后找到
If Ollama is on your computer, use this command:


![install-webui](http://hdj2048228.github.io/img/2025-02/open_webui.png)

点击复制代码，打开命令行，同样用管理员身份进行下载，即

```
sudo docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main\
```



等待下载完成
到电脑右上角找到小船图标，点击Goto the dashboard

![install-webui](http://hdj2048228.github.io/img/2025-02/docker_open_webui.png)

此时就可以看到open-webui安装好了，点击Ports中的箭头就可以打卡对话界面
在右上角可以选择安装的大模型进行应用。

![install-webui](http://hdj2048228.github.io/img/2025-02/docker_open_webui.png)


open-webui示意图

![install-webui](http://hdj2048228.github.io/img/2025-02/docker_open_webui_dashboard.png)


点击左上角可切换大模型

![change_model](http://hdj2048228.github.io/img/2025-02/change_model.png)


now say hello world

![say_hello](http://hdj2048228.github.io/img/2025-02/say_hello.png)

![say_hello_comond](http://hdj2048228.github.io/img/2025-02/say_hello_comond.png)
