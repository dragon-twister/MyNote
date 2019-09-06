# Linux常见命令
    
[toc]

### 常见命令
ps -ef|grep 

cat 查看文本文件的内容

tail 显示文件最后几行

free netstat -apn|gtep 端口 查看端口占用情况

sudo 可以让用户在不必登陆root帐号的情况下，使用root权限。

数据库: top showprocesslist  mysqladmin kill 

### sudo chmod 777 代表什么：

Linux的文件调用权限分为三级 : 文件拥有者、群组、其他。

利用 chmod 可以控制文件如何被他人所调用。

三位数字分别代表 user、group、others 的权限，

可读(r)、可写(w)、可执行(x)的权限分别用数字 4、2、1 表示，数字7是权限 4、2、1 的和

777 即代表 user、group、others均具有可读(r)、可写(w)、可执行(x)的权限，为最高权限。



### 常见端口

smtp 25 

ftp 21 

ssh 22

http 443 

mysql 3306

redis 6379

### linux创建用户

useradd testuser  创建用户testuser

passwd testuser  给已创建的用户testuser设置密码

说明：新创建的用户会在/home下创建一个用户目录testuser

usermod --help  修改用户这个命令的相关参数

userdel testuser  删除用户testuser

rm -rf testuser  删除用户testuser所在目录


![image](http://s3.sinaimg.cn/middle/7f2122c5gbe05211fe252&690)




1 . 概述

netstat 显示各种网络详细信息。 如TCP UDP 路由器等相关信息

lsof: 先要理解 在LInux环境下， 任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等，系统在后台都为该应用程序分配了一个文件描述符，无论这个文件的本质如何，该文件描述符为应用程序与基础操作系统之间的交互提供了通用接口。因为应用程序打开文件的描述符列表提供了大量关于这个应用程序本身的信息，因此通过lsof工具能够查看这个列表对系统监测以及排错将是很有帮助的。

lsof（list open files）是一个列出当前系统打开文件的工具

  

2\. 详细描述

netstat

netstat 在不指定参数的时候不显示PID

netstat -a 显示所有socket，包括正在监听的。(默认不显示LISTEN相关)  

netstat -at列出所有TCP链接

netstat -au 列出所所有UDP链接

netstat -l 列出所有监听状态的SOCKET

netstat -p 显示PID， 可以和别的参数合并使用 netstat -pt

lsof

lsof filename 显示打开指定文件的所有进程  

lsof -u username 显示所属user进程打开的文件  

lsof -i 用以显示符合条件的进程情况  

  

综合：

根据端口port查进程

   netstat -nap | grep port

      lsof  -i:port  

  
根据进程pid查端口  

 netstat -nap | grep pid

 lsof -i | grep pid
