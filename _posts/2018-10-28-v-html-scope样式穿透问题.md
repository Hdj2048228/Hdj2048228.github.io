---
layout:     post
title:      v-html 里的html标签不能继承外部的css解决方式
subtitle:   
date:       2018-10-28
author:     hdj
header-img: img/bgs/girl-3.jpg
catalog: true
categories : [v-html，样式穿透]

tags:
    - v-html
    - vue
---






**1. 去掉scoped属性**

`<style lang="less" scoped>  </style>
`

**2. 在写样式的时候添加  >>>**  

`.box >>> p { color: red; }
`

[参考： 用v-html渲染页面的时候 css样](http://www.mamicode.com/info-detail-2404898.html
)







