---
title: Linux常用的命令
date: 2019-08-18 09:22:47
tags: Linux 
categories: 操作系统
---

# 删除目录下的TXT文件用什么命令

rm -f 文件名

# 新建一个文件用什么命令

要创建一个名为ll的文件，那么输入：【touch  ll】

# 新建一个目录用什么命令

要创建文件夹，那么命令修改为：【mkdir】+文件夹名即可

# 看端口

ps -aux | grep tomcat

netstat –apn：查看所有的进程和端口使用情况

# 看被占用的端口的进程

netstat -tunpl |grep 端口号

# 查看进程的详细信息

ps -ef|grep 进程ID

# 查看所有进程

ps -ef 

# 查看一个进程的端口号：

如果知道进程ID的话就直接利用：netstat -anp | grep 进程ID 就可以查询出来了。不然就用ps命令查看进程ID：ps -ef | grep 进程名 

#  显示CPU的信息 ，查看CPU使用率

cat /proc/cpuinfo

#top -bn 1 -i -c（top 命令可以看到总体的系统运行状态和 cpu 的使用率）

# 查看目录中的文件 

ls

# 查看内存使用情况

free

输出内容的解释：

- total:总计物理内存的大小。
- used:已使用多大。
- free:可用有多少。
- Shared:多个进程共享的内存总额。
- Buffers/cached:磁盘缓存的大小。

# 查询进程内部当前运行了多少线程

#pstree -p 19135（进程号）

# 使用top命令查看（可以查看到线程情况）

top -Hp 19135  