---
layout: post
title: 重装windows后 与ubuntu双系统引导修复  
category: 技术
tags: 双系统
keywords: 
description: 
---  
**这里主要记录下整个过程**  
之前一直是win8.1 ubuntu14.04双系统 用grub来引导两个系统 后来用U盘升级了win10后，当时记得重启后两个系统都不能进了 后来用了一个叫NTBOOTautofix的软件自动修复好了 能进入win10了 但是开机却非常慢  

后来又想要用下ubuntu就想修复下

首先用easybcd软件来添加linux的引导项 结果开机时出现多个系统的选择界面了 进去却提示找不到 这样又过了几天  

后来偶然间用新发现的一个搜索引擎[脚本百事通][1] 搜到了这么一个[帖子][2]  
按照这样的步骤：
- 做了个ubuntu启动U盘  
- 选择试用系统进入ubuntu，ctrl+alt+t 打开终端，输入 `sudo fdisl -l` 在给出的系统盘符信息中找到Linux所在的盘符，如我的是 `/dev/sda10`
- 再输入`sudo -i`获取root权限
- `mount /dev/sda10 /mnt` 注意这里`/mnt`前要有空格
- 输入 `grub-install --root-directory=/mnt /dev/sda`

到现在按照他的说法是Grub基本修复完毕

- 重启后输入`sudo update-grub2`  

到这里本以为搞定了 没想到进入windows时提示需要修复 感觉可能是因为之前用easybcd又搞了个由windows来引导的 进不了 又试了各种方法：
- 修改grub.cfg文件 ·······failed  

最后试了windows高级选项里的每一个，通过系统还原将系统还原到了之前的一个版本，它本来提示还原遇到错误，但重启后还是成功进入了，但依旧很慢。  

莫名其妙的就又好了 不明白什么原理 记录下 以后细看研究下...




[1]:  http://www.csdn123.com/
[2]:http://www.csdn123.com/html/mycsdn20140110/7f/7ff3956602fd9d564c9a5820910f51db.html
