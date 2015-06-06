---
layout: post
title:  "在redhat下服务式管理Oracle的启动和关闭"
date:   2014-11-03 22:06:32
categories: oracle
---
1. 修改Oracle系统配置文件：/etc/oratab
只有这样，Oracle 自带的dbstart和dbshut才能够发挥作用。
{% highlight ruby %}
# vi /etc/oratab
orcl:/opt/oracle/102:Y
# Entries are of the form:
#   $ORACLE_SID:$ORACLE_HOME:<N|Y>:
2. 在 /etc/init.d/ 下创建文件oracle，内容如下：
#!/bin/sh
# chkconfig: 35 80 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
# from which you wish to execute dbstart and dbshut;
#
# Set ORA_OWNER to the user id of the owner of the
# Oracle database in ORA_HOME.
ORA_HOME=/opt/oracle/102
ORA_OWNER=oracle
if [ ! -f $ORA_HOME/bin/dbstart ]
then
    echo "Oracle startup: cannot start"
    exit
fi
case "$1" in
'start')
# Start the Oracle databases:
echo "Starting Oracle Databases ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Starting Oracle Databases as part of system up." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart" >>/var/log/oracle
echo "Done"
# Start the Listener:
echo "Starting Oracle Listeners ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Starting Oracle Listeners as part of system up." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl start" >>/var/log/oracle
echo "Done."
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Finished." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
touch /var/lock/subsys/oracle
;;
'stop')
# Stop the Oracle Listener:
echo "Stoping Oracle Listeners ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Stoping Oracle Listener as part of system down." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/lsnrctl stop" >>/var/log/oracle
echo "Done."
rm -f /var/lock/subsys/oracle
# Stop the Oracle Database:
echo "Stoping Oracle Databases ... "
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Stoping Oracle Databases as part of system down." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut" >>/var/log/oracle
echo "Done."
echo ""
echo "-------------------------------------------------" >> /var/log/oracle
date +" %T %a %D : Finished." >> /var/log/oracle
echo "-------------------------------------------------" >> /var/log/oracle
;;
'restart')
$0 stop
$0 start
;;
esac
{% endhighlight %}
3. 改变文件权限
{% highlight ruby %}
>chmod 755 /etc/init.d/oracle
{% endhighlight %}
4. 添加服务
{% highlight ruby %}
>chkconfig --level 35 oracle on
{% endhighlight %}
5. 需要在关机或重启机器之前停止数据库，做一下操作
{% highlight ruby %}
>ln -s /etc/init.d/oracle /etc/rc0.d/K01oracle   //关机
>ln -s /etc/init.d/oracle /etc/rc6.d/K01oracle   //重启
{% endhighlight %}
6. 使用方法

{% highlight ruby %}
>service oracle start        //启动oracle
>service oracle stop        //关闭oracle
>service oracle restart     //重启oracle
{% endhighlight %}