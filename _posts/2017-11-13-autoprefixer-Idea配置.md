---
layout:     post
title:      Mac 下 autoprefixer配置
subtitle:    Mac 下 autoprefixer配置
date:       2017-11-13
author:     hdj
header-img: img/2017-11/idea-bg.jpg
catalog: true
tags:
    - autoprefixer配置
    - Idea
    - Mac
---
# 前言


CSS3不同平台兼容性增加了很多代码，人工实现兼容费时费力又容易出错,例如

```
-webkit-transform: ;
-moz-transform: ;
-ms-transform: ;
-o-transform: ;
transform: ;
```

对于我这种半吊子水平的iOS转web的好吃蓝做的码畜，该怎么办？

答曰：使用autoprefixer


# autoprefixer简介
autoprefixer可以实现css3代码自动补全。此处省略一大堆有的没得（为了证明我是真的懒）。

详情见，[autoprefixer的githup。](https://github.com/postcss/autoprefixer)

### 1. 安装autoprefixer
   使用NPM 安装 没有NPM？去gitHup上看看把，总能下载到的。
   ```
   安装 npm install autoprefixer -g   // 是的下载全局的
   ````
### 2. 安装postcss-cli
```
Autoprefixer其实是postcss的插件，见[postcss-cli gitHub](https://github.com/code42day/postcss-cli)
 npm install postcss-cli -g
```
 （下载慢的话使用淘宝镜像 https://npm.taobao.org/ cnpm安装 这个可以百度一下）
### 3.配置IDEA External Tools
打开Webstorm设置，Preferences -> Tools -> External Tools ;点击新增按钮，如图：
![idea-autoprefixers](http://hdj2048228.github.io/img/2017-11/idea-autoprefixers.png)

Program:填入你的postcss-cli 的PATH，下面是我的文件路径

```
 /usr/local/bin/postcss
```
最好看一下又没有这个文件:

![idea-postcss](http://hdj2048228.github.io/img/2017-11/idea-postcss.png)

Parameters:

```
 -u autoprefixer -o $FileDir$/$FileName$  $FileDir$/$FileName$
```
 你可以根据你自己的需要配置，具体参见 [postcss-cli gitHup](https://github.com/code42day/postcss-cli)

Working directory :

```
$ProjectFileDir$
```

配置好后，你可以在css，或sass文件中右键，就可以在右键菜单中看到External Tools – autoprefixer，点击搞定。
### 4.设置快捷键

   右键太麻烦的话，可以设置个快捷键，打开Webstorm设置，Preferences -> Keymap ， 搜索External Tools ，
    配置 autoprefixer即可。 不要和原来的冲突就可以了。(我用的是option+A。)



####  另附windows的配置方式

![idea-postcss](http://hdj2048228.github.io/img/2017-11/idea-autoprefixer-windows.png)
