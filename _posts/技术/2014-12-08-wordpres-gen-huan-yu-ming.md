---
layout: post
title: wordpress更换域名
category: 技术
tags: 域名 wordpress
keywords: 
description: 
---
昨晚把之前用wordpress搭建的博客换了域名，只是在域名解析的时候做了不同的转向 具体的数据库都忘了改  
当时浏览着还没问题 今天一看 整个乱了 点击后还回跳转到之前的域名

在网上搜了下将wordpress数据库中的地址变成新域名  
我用的虚拟空间是用的cpanel面板 可以直接进去phpMyadmin里面执行两条sql语句


更改博客的安装地址和博客地址  
```sql
UPDATE wp_options   
SET option_value = replace( option_value,'http://老域名', 'http://新域名')  
WHERE option_name = 'home' OR option_name ='siteurl';
```

![](/public/img/wp-1.png)



只执行这条语句后发现一些图片什么的不能正常显示  
还需要修改文章内部所有的链接为新域名  
> `UPDATE wp_posts SET post_content = replace(post_content,'http://老域名','http://新域名');`
`UPDATE wp_posts SET guid = replace( guid,'http://老域名','http://新域名');`


![](/public/img/wp-2.png)
