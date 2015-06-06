---
layout: post
title:  "OSX下的虚拟机（redhat6.5）上装Oracle 11g R2教程"
date:   2014-10-30 10:54:00
categories: oracle
---
#环境

本机：osx Yosemite 10.10

虚拟机：VMware Fusion

Linux：redhat6.5 64 位

Oracle：Oracle 11g R2

#准备

1. 安装redhat6.5操作系统，并下载oracle安装包到redhat。（可先在本机下载，再传到redhat）
oracle 下载地址：http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html
我是选择“Oracle Database 11g Release 2” – “Linux x86-64”，一共2个zip压缩文件，到redhat一起压缩合并。
2. 安装oracle依赖包，很多包系统已经集成，只需要新安装如下7个包即可，其中6个在redhat镜像盘的package里面都有。
compat-libstdc++
elfutils-libelf-devel
elfutils-libelf-devel-static (自带package里面没有，可根据版本自行搜索下载此包)
ksh
libaio-devel
libstdc++-devel
unixODBC-devel
3. 创建”oracle”用户
在root下：创建Oracle安装组“oinstall”,数据库管理员组“dba”，及“oracle”用户，脚本如下：
# /usr/sbin/groupadd oinstall
# /usr/sbin/groupadd dba
# /usr/sbin/useradd -g oinstall -G dba oracle（主组oinstall，其它组：dba）
# passwd oracle (设置系统的oracle用户密码)
4. 修改系统内核参数，具体脚本如下
root用户下：修改 /etc/sysctl.conf 文件，加上如下参数
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = 2097152
kernel.shmmax = 536870912
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048586
为使上述配置生效而不重启系统，执行如下命令
# /sbin/sysctl -p
5. 修改用户限制
root用户下：修改 /etc/security/limits.conf 文件，加上下面的参数
oracle           soft    nproc   2047
oracle           hard    nproc   16384
oracle           soft    nofile  1024
oracle           hard    nofile  65536
6. 修改用户验证选项
root用户下：修改/etc/pam.d/login文件加上如下参数
session    required     pam_limits.so
7. 修改用户配置文件
root用户下：修改/etc/profile文件加入如下参数：
if [ $USER = &quot;oracle&quot; ]; then
    if [ $SHELL = &quot;/bin/ksh&quot; ]; then 
        ulimit -p 16384
        ulimit -n 65536
    else
        ulimit -u 16384 -n 65536
    fi
fi
8. 安装目录配置
root用户下：
# mkdir -p /u01/oraInventory
# chown -R oracle:oinstall /u01/
# chmod -R 775 /u01/
9. 修改用户bash shell
这个是用户启动自动设置的环境变量，可根据启动用户进行设置，此处设置root用户，依然在root用户下：
$ vi .bash_profile
增加如下内容，主要是修改，根据oracle的安装路径进行配置
export ORACLE_BASE=/u01
export ORACLE_HOME=$ORACLE_BASE/oracle
export ORACLE_SID=oracleSid
export PATH=$ORACLE_HOME/bin:$PATH:$HOME/bin
export LANG="zh_US"
完成后执行：
#su oracle
#env | grep ora     (查看环境变量是否完成)

#安装Oracle
1. 使用oracle用户登陆（图形界面），并解压oracle安装包
$unzip linux.x64_11gR2_database_1of2.zip
$unzip linux.x64_11gR2_database_2of2.zip
#再将解压的database合并
再进入解压的database目录执行
$ ./runInstaller
此时就会弹出按照常规安装方法进行安装..
安装完后可用以下命令进行监听的配置
$ netca
配置完成后可用下面命令进行监听是否启动检查
$ ps -ef|grep listener
如果安装过程中没有创建数据库可用以下命令进行创建
$ dbca
配置完成后用下面命令进行启动检查
$ ps -ef|grep oracle

#测试
这个时候我们就可以用sqlplus进行测试：
$sqlplus /nolog 
SQL>conn / as sysdba 
SQL>create table mytable(name varchar(10), age int); 
SQL>insert into mytable values(‘user1’,12); 
SQL>select * from mytable;
如果在进行连接时候出现如下消息(如下图)
Connected to an idle instance.
![Alt text](http://7u2srn.com1.z0.glb.clouddn.com/@/images/post//vmware-redhat-oracle_01.png)
那么就是oracle并没有开启，执行
SQL> startup
即可。

#后记
本文主要记录在redhat下安装oracle的基本过程，后面我会写一篇利用存命令行来使用oracle，让我们的虚拟机占用内存降低，以及把开启oracle写成服务随着用户登陆自动开启。


​