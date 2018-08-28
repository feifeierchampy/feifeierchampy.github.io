---
layout: post
title: 利用Github免费空间创建个人静态网站
category: 技术
tags: 网站
keywords: 
description: 
--- 
  
>You never know what you've had, until its gone.                                                           
---
﻿

[toc]


### 1. 域名解析

这里以添加一个二级域名 `timeline.feifeier.com` 为例
需要在你买的域名提供商里的管理面板里添加域名解析
![1](/images/2018082801.png)




### 2. Github上新建一个仓库

![2](/images/2018082802.png)




### 3. 本地clone下来，并新建gh-pages分支

git clone

```

git clone git@github.com:feifeierchampy/webpage-timeline.git

```



新建gh-pages分支

```

git branch gh-pages

```

![3](/images/2018082803.png)




### 4. 添加代码，CNAME文件

CNAME文件

```

timeline.feifeier.com

```

静态网页的代码



















