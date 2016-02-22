---
layout: post
title: 关于Github pages或Project pages绑定自己域名的问题
category: 技术
tags: 域名
keywords: 
description: 
---
无论前者还是后者 都在域名解析里添加一个cname指向  
github会自动的根据你项目里的CNAME文件来解析
###Github Pages
####github pages(`username.github.io`)只能创建一个，比如我这个  
####（未绑定之前可通过`http://feifeierchampy.github.io`访问）  
####`feifeierchampy.github.io`  想把`love.feifeier.com`绑定到上面  
####解析的时候就在DNS解析里添加一个CNAME类型的  
![](/public/img/yuming.png)  
####而在Github这边 就在`feifeierchampy.github.io`的`master`目录下添加一个`CNAME`文件（内容为love.feifeier.com）  

###Project Pages
如果是Project Pages（项目名为`MyFirstLover`）     
（未绑定之前可通过`http://feifeierchampy.github.io/MyFirstLover`访问）  
在你自己的域名注册商的DNS解析这边不变也是`CNAME`对应到`feifeierchampy.github.io` 
而在Github这边 要在`gh-pages`下添加CNAME文件（`love.feifeier.com`)即可

这样看来 这两种都是一样的 在你的域名提供商的域名解析里都只要添加一个CNAME  
对应到`feifeierchampy.github.io`就行了  
而在Github这边会自动的根据每个项目里的CNAME文件里的内容来决定转向哪里 

添加之后 Github会有一段反应时间 不一定 有时10分钟 有时半个小时  
之前看好久了还404还以为自己弄错了 耐心等待就好
