---
layout: post
title: Note on Linux
category: OS
description: 记录一些linux维护方面踩过的坑
---

Several Notes on my route to OP  
1. 软链接 ln -s source_link target  
2. Program get killed， check the system log, in /var/log/message(centos), or /var/log/syslog(ubuntu)    
3. 添加用户和密码 useradd -m -G group_name user_name;  passwd user_name (-m 表示建立home目录)  
4. 给用户添加权限 visudo； user_name ALL = (ALL:ALL) ALL  
5. 有时候你的服务器(Django)外部不能访问，请查看一下是否有bind address，如果有，请设置为__0.0.0.0__(全网络)，或者合适的ip地址  
6. 新建账户时，有可能退格，tab补全等没有用，请echo $0 如果不是“/bin/bash”的话，“exec /bin/bash” 切换过去。  
7. 查看系统内核，可能是x86，或者Power，或者arm 之类的：uname -a  
8. 查看*unix的发行版本： lsb_release -a  
9. centos 查看系统安装的rpm， rpm -qa | grep xxx  
10. swap 0 solution

```shell
	Run **dd if=/dev/zero of=/swapfile bs=1M count=1024**  
	Run **mkswap /swapfile**  
	Run **swapon /swapfile**  
	Add this line __/swapfile swap swap defaults 0 0__ to __/etc/fstab__  
	some useful command:  
	$ swapon -s     
	$ free -k  
	$ swapoff -a  
	$ swapon  -a  
	$ chmod 0600 /swapfile
```



11. 排查crontab问题，如果某个user建立了cron job，那么在**/var/spool/cron/crontabs/ ** 中会找到以这个user命名的文件和他对应执行的命令。  
12. ubuntu 使用ss  

```shell
pip install shadowsocks
# client的命令是sslocal 服务器端是ssserver
sslocal -c ~/ss.json --user cdmaok -d start
sslocal -d stop
# 在/var/log/shadowsocks.log中排查错误
```



13. max file descriptors 的设置方法：修改**/etc/security/limits.conf**,添加 [user_name] hard nofile 102400 或者 * hard nofile 102400 即可。  

14. linux 查看网络配置 **ifconfig**(wired) 或者 **iwconfig**(wireless)  

15. 修改机器名称 vi /etc/hostname 和 /etc/hosts

16. ![Linux map](http://7xpv97.com1.z0.glb.clouddn.com/dffcef97007537585d1745e2b40a4b55.png)

17. shell script show the current path:

```shell
#!/bin/bash
basepath=$(cd `dirname $0`; pwd)
echo $basepath
```

18.  多版本共存，有的时候机器里面某一个软件多于一个版本，下面以java为例讲解alternative

```shell
## 首先去/usr/bin里面看看链接的指向是哪里，如果是指向/ect/alternative才可以这样做
update-alternative --config Java
## 然后选择你要的版本即可
## 如果没有需要的版本需要自己安装。
alternatives --install <link> <name> <path> <priority>
## 这里link 是指链接，如/usr/bin/Java, name = java, path是你安装的路径，priority 填60，表示最高。
```

19. 操作系统默认 240 秒后，才会关闭处于 time_wait 状态的连接，在高并发访问下，服
务器端会因为处于 time_wait 的连接数太多，可能无法建立新的连接，所以需要在服务器上
调小此等待值。在 linux 服务器上请通过变更/etc/sysctl. conf 文件去修改该缺省值（秒） ：net. ipv4. tcp_fin_timeout = 30

20. 给 JVM 设置-XX:+HeapDumpOnOutOfMemoryError 参数，让 JVM 碰到 OOM 场景时输出
dump 信息。

21. 学会用parallel -pipe 命令， 在管道中可以有效的加速
