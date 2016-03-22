---
layout: post
title: Linux学习记录
category: 技术
tags: Linux
keywords: 
description: 
---  


[toc]

### Linux硬盘概念
- 硬盘采用hdX表示，X可为a,b,c,d。
- 系统最多只有4个IDE设备，第一个IDE设备名称为hda,第4个为hdd。
- 一个硬盘最多只能有4个主分区
+ 主分区采用hdXN的格式，hdX为硬盘，N是1-4的数字，分别表示4个主分区，第一个硬盘主分区为hda1，以此类推。
- 扩展分区作为特殊的主分区需要占用硬盘分区表中4个分区记录中的1个记录
- 逻辑分区：逻辑分区只能建立在扩展分区中，可以建立文件系统，逻辑分区同样采用hdXN的格式，但是从5开始算。第一个硬盘的第二个逻辑分区就为hda6。

### linux命令格式
短格式 `-l`
长格式 `--help`
组合命令选项  `-a -l` 组合成 `-al` 或 `-la`

####常用命令

##### 查看/设置环境变量
- `echo $PATH`
- `export PATH=$PATH:/home/user/work/` 新添加一个环境变量/home/user/work/ 该更改只是在此次登录有效， /etc/profile里更改长期有效

#####文件相关
- `touch` 新建文件命令
- `cat` 文本文件查看（不能分屏）
- `more` 文本文件查看 （能分屏显示）
- `less` 文本文件查看 （能分屏，方便反复浏览）
- `head -数字` 显示文件首部指定行的内容
- `tail -数字` 尾部...
- `du` 查看指定目录的大小 `du -sh newFile/`  显示为`24K     newFile/`
- `mv`重命名    `mv hello hello_rename`
- `file`查看文件类型  `file helloworld.txt`

##### whereis
- `whereis date` 查看命令所在路径

##### 查找文件 find
- `find /user -name "time.c"` 即`find 目录 -name "文件名"` 其中文件名可使用通配符匹配
- `find . -type f -name "my"` 只查找普通文件
- `find . -type d -name "my" 只查找目录
- `find -iname`不区分大小写

#####获取root权限
- `sudo -i` 可以输入当前管理员用户的密码就可以进到root用户
- `sudo passwd root` 要一直使用root权限，首先要重新设置root用户的密码
之后就可以自由的切换到root用户了。su 输入root用户密码即可

- 从超级用户回到普通用户
    + `ctrl +d ` 回到原来的用户
    + `su userName` 直接转到那个用户 无需密码

#####挂载
- `mount` 查看当前已经挂载的设备
- `mount -a` 依据配置文件`/etc/fstab的内容自动挂载
- `mount -o remount,rw /system`   将system分区重新mount为读写状态，`-o` options主要用来描述设备或档案的挂载方式，常用参数有
    + loop: 用来把一个文件当成硬盘分区挂接上系统
    + ro: 采用只读方式挂载设备
    + rw: 采用读写方式挂载设备
    + iocharset: 指定访问文件系统所用字符集

#####进程相关命令
- `ps` 简单显示当前进程
    + `ps aux` 查看系统内所有进程
    + `pstree` 查看进程树，可显示进程与子进程的详细列表
- `top` 全屏显示进程信息
    + q键 退出 
    + P键 按CPU排序
    + N键 按打开时间排序你
    + A键 按PID号排序

- `Ctrl + c` 结束当前进程
- 在后台启动进程：命令后加&
- 将后台程序调用终端前台执行： fg 后台程序名

- 切换用户 `su -u 用户名`

#### 快捷键
<img class="my_img" src="{{site.img_path}}/linux-hotkey.jpg">

####使用USB存储设备
USB接口的移动硬盘、U盘对Linux系统而言是当作SCSI设备对待的
1. 识别U盘 `/dev/sda` `/dev/sdb`
    `#fdisk -l`
2. 使用mount命令挂载U盘
    `#mount -t vfat /dev/sda1 /mnt/`
3. 通过挂载点访问U盘的内容
    `#ls /mnt`
4. 使用umount命令卸载U盘
    `#umount /mnt`
####Shell

#####管道
`l`符用于连接左右两个命令，将`l`左边的命令执行结果（输出）作为`l`右边命令的输入。
如`$cat /etc/passwd | grep lrj`

**shell脚本由Shell环境解释执行，不需要在执行前进行编译**

- 脚本运行环境设置 如： `#!/bin/bash`

### 文件权限
- `drwxrwxr-x 10 liuchang liuchang     4096 Feb 18 16:45 p_rtd2997/` 第一个`liuchang`表示文件的owner，第二个`liuchnag`表示owner所在的组group

- `.file` 文件名带`.`表示隐藏文件
- `----------` 10位 
    + 第一位 `-`代表文件 `d`表示目录
    + 后面每3位为一组，分别表示Ownner Group Others
    + `r`可读(如可使用ls命令)  `w`可写(可使用touch命令) `x`可执行(对目录来说就是可进入cd命令)
    + 有该权限则用相应字母表示，没有则用`-`表示
#### chmod
- `chmod`修改权限 有对应1，无对应2。`rwx`则为7，`r-x`则为5，
- `chmod -w mydir` 去掉所有可写权限

### 压缩/打包

#### tar 
- `tar cf mytar.tar test my.sh` 将test目录，my.sh文件打包为mytar.tar （并没有改变文件大小的作用）
- `tar -tvf mytar.tar` 显示打包内容 显示为：

``` shell
drwxrwxr-x liuchang/liuchang 0 2016-02-24 10:58 test/  
-rw-rw-r-- liuchang/liuchang 85 2016-02-24 10:58 test/my.sh  
-rw-rw-r-- liuchang/liuchang 11 2016-02-24 10:58 test/my.txt  
-rw-rw-r-- liuchang/liuchang 85 2016-01-28 14:29 my.sh  
```
	
- `tar cjf mycompress.tar.bz2 test my.sh` j表示采用bz2压缩工具

- 解压 `tar xf mytar.tar -C my_tar` -C表示输出目录（目录首先要存在）

#### diff 比较两个文件的不同

- `diff -r test my_tar` 比较两个目录的不同 -r表示递归

### Vim
- `esc`  `:%!xxd`查看二进制码
    + `0000000: 6865 6c6c 6f20 6861 6861 6861 0a         hello hahaha.`
    