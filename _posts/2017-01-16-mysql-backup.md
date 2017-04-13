---
layout: post
title: MySQL 数据库备份
tags: mysql
author: zengyilun
---
## 热备与冷备
数据库的备份与恢复是一项最基本但是却不简单的工作，根据不同类型来划分备份的方法，划分出最基础的两种备份，热备与冷备。冷备是指在数据库停止的情况下，一般只需要复制相关数据库文件，而热备即在数据库运行的时候直接备份，对正在运行的数据库没有任何影响。 
<!-- more -->


以下通过两种备份来保证数据的安全，复制备份与快照备份。复制备份用于数据库实时备份，当开发人员对主数据库进行误操作时，其结果也会应用到从数据库，所以需要对从数据库进行快照(snapshot)来防止从数据库数据丢失。

## 1. 复制备份
同大多数关系型数据库一样，日志文件是MySQL数据库的重要组成部分。MySQL有几种不同的日志文件，通常包括错误日志文件，二进制日志，通用日志，慢查询日志，等等。这些日志可以帮助我们定位mysqld内部发生的事件，数据库性能故障，记录数据的变更历史，用户恢复数据库等等。  

其中二进制日志可用于MySQL数据库的备份，通过一个完全备份进行二进制日志的重做来完成数据库的point-in-time的恢复工作，MySQL数据库复制(replication)的原理就是异步实时地将二进制日志重做传送并应用到从数据库。

MySQL 复制是MySQL数据库提供的一种高可用高性能的解决方案，复制的原理并不难，其实就是一个完全备份加上二进制日志备份的还原。

## 2. 快照备份
`Logical Volume Manager (LVM)`提供了对任意一个`Logical Volume(LV)`做“快照”(snapshot)的功能，以此来获得一个分区的状态一致性备份。
在某一个状态下做备份的时候，可能有应用正在访问某一个文件或者数据库，这就是使得备份的时候文件处于一个状态，而备份完后，文件却处于另外一个状态，从而造成备份的非一致性，这种状态恢复数据库数据几乎不会成功。

状态的解决办法是将其分区挂载为只读，然后通过数据库的表级别锁定(table-level write locks)甚至停止数据库来备份数据。所有这些方法无意严重影响了服务的可用性。使用LVM snapshot既可以获得一致性备份，又不会影响服务器的可用性。

## 3. 开始备份

#### 1.准备工作
有两台服务器`192.168.2.5`与`192.168.2.230`，在同一局域网，为了快速部署并且不影响操作系统，将使用`docker`作为运行`mysql`的容器。  
从`docker hub`拉取或者自己制作一个镜像，这里已经在`192.168.2.5`制作好了一个镜像，并已经push到了`192.168.2.230`。

开始在`192.168.2.5` **运行MySQL**:

    docker run -d -h mysql-server --restart=always --name=mysql_server -p 3306:3306 -v /mnt1/data/mysql:/mnt/data/mysql-online 192.168.2.230:5000/sunyuki/mysqllinux:latest
    
这样会运行一个`docker`容器，并且将`docker`中的`mysql`数据库文件`mnt/data/mysql-online`挂载到了本地磁盘`/mnt1/data/mysql`。
    
同样的，在`192.168.2.230`执行相同的命令，这样在两台机器的运行的`mysql`是一样的，并且两个容器的名称是`mysql_server`。
    

    
#### 2.启用数据库二进制日志
因为复制的原理是重做二进制日志，所以需要为两台机器都启用二进制日志。

两台机器都运行起`docker`容器后，连接到`192.168.2.5`进入`mysql_server`容器更改配置文件:

    docker exec -it mysql_server /bin/bash


在数据库配置文件中设置(通常为`/etc/my.cnf`):
    
    [mysqld]
    log-bin = mysql-bin
    sync_binlog = 1
    innodb_support_xa = 1
    
连接到`192.168.2.230`做同样的操作。
    
这样两台机器都启用了二进制日志。

#### 2.更改数据库 server-id
打算将`192.168.230`作为**从服务器**，`192.168.2.5`作为**主服务器**，需要将两台机器的数据库`server-id`更改的不一样。  

在`192.168.2.230`:  
进入docker容器:

    docker exec -it mysql_server /bin/bash
    
更改数据库配置文件:
    
    [mysqld]
    server-id = 2
    
并且重命名`192.168.2.230`中的 `auto.cnf`:

    mv auto.cnf auto.cnf.bak
    
重命名这个文件的原因是这个文件定义了两台服务器的`uuid`，因为两台服务器的`docker`镜像是一样的，所以要让`mysql`重新生成`uuid`。

`auto.cnf`中的内容一览:

    [auto]
    server-uuid=9f31bddf-d965-11e6-9c77-0242ac110003

重启`mysql`
    
    service mysql restart

在`192.168.2.5`:  
进入docker容器:

    docker exec -it mysql_server /bin/bash
    
更改数据库配置文件:
    
    [mysqld]
    server-id = 1

重启`mysql`

    service mysql restart
    
#### 3.配置主从数据库
在`192.168.2.5`:  
进入`192.168.2.5`的`docker`容器:  
创建新用户用于复制功能:
    
    mysql> CREATE USER 'repl'@'192.168.2.230' IDENTIFIED BY 'repl';
    mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.2.230';
    
如果想看一下结果，可以查看`mysql`用户

    mysql> select user,host from mysql.user;

首先查询主数据库日志文件位置:  

    mysql> show master status
    
    | file             | position |
    | mysql-bin.000017 | 120      |
    
获取到两个重要的信息，二进制日志文件名与执行位置  


在`192.168.2.230`:  
进入`192.168.2.230`的`docker`容器:  
配置从服务器:

    mysql> CHANGE MASTER TO
    MASTER_HOST ='192.168.2.5',
    MASTER_USER ='repl',
    MASTER_PASSWORD ='repl',
    MASTER_LOG_FILE ='mysql-bin.000017',
    MASTER_LOG_POS =120;

其中`MASTER_LOG_FILE`与`MASTER_LOG_POS`要与主数据库中的`show master status`的结果一致。

开启从数据库:
    
    mysql> start slave;
    
查看是否配置成功:

    mysql> show slave status;
    
    | Slave_IO_Running | Slave_SQL_Running | Last_IO_Error | Last_SQL_Error|
    | yes              | yes               |               |               |
    
如果`Slave_SQL_Running`与`Slave_IO_Running`其中一个不是`yes`，就没有配置成功，具体错误可以在`Last_IO_Error`和`Last_SQL_Error`中查看。

依据这个原理，可以写一个脚本用户实时的去检查主从数据库是不是出错，如果出错了就发邮件给管理者，这里使用的是`python`写的脚本。
    
安装`mysql-connector for python`:

    wget https://dev.mysql.com/get/Downloads/Connector-Python/mysql-connector-python-2.1.5-1.el6.x86_64.rpm
    rpm -i mysql-connector-python-2.1.5-1.el6.x86_64.rpm
    
安好connector后开始写`python`脚本:

```python
#!/usr/bin/python
import mysql.connector
import logging
import time
import smtplib
from email.mime.text import MIMEText

# mysql
HOST = '127.0.0.1'
USER = 'xxx'
PASSWORD = 'xxxx'
LOG_FILE = '/var/log/mysqlchk.log'
TIME_GAP = 120  # interval time gap (unit seconds)

# smtp
mailto_list = ["me@qq.com"]
mail_host = "smtp.domain.com"
mail_user = "xxx"
mail_pass = "xxx"
mail_postfix = "domain.com"
mail_nick_name = 'MySQL CHK'
mail_subject = '[ERROR] MySQL Replication Error'

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)
# create a file handler
handler = logging.FileHandler(LOG_FILE)
handler.setLevel(logging.INFO)
# create a logging format
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
# add the handlers to the logger
logger.addHandler(handler)

logger.info('program start')
cnx = mysql.connector.connect(user=USER, password=PASSWORD, host=HOST)


def send_mail(to_list, sub, content):
    me = mail_nick_name + "<" + mail_user + "@" + mail_postfix + ">"
    msg = MIMEText(content, _subtype='plain', _charset='gb2312')
    msg['Subject'] = sub
    msg['From'] = me
    msg['To'] = ";".join(to_list)
    try:
        server = smtplib.SMTP()
        server.connect(mail_host)
        server.login(mail_user, mail_pass)
        server.sendmail(me, to_list, msg.as_string())
        server.close()
        return True
    except Exception, e:
        print str(e)
        return False


try:
    def check():
        logger.info('start check slave status..')
        cur = cnx.cursor()
        query = ("show slave status")
        cur.execute(query)
        res = cur.fetchone()
        (slave_io_running, slave_sql_running, last_io_error) = (res[10], res[11], res[35])
        if slave_io_running != 'yes' and slave_sql_running != 'yes':
            error = '[ERROR]Slave Error:{0}'.format(last_io_error);
            logger.error(error)
            send_mail(mailto_list, mail_subject, error)
        cur.close()


    while True:
        check()
        time.sleep(TIME_GAP)
except Exception, e:
    logger.error(str(e))
    send_mail(mailto_list, mail_subject, str(e))
finally:
    cnx.close
    logger.info('program terminate')
```

这个脚本的用处就是每两分钟去检测主从服务器连接是否正常，如果不正常将会发送错误消息给管理员。  
到这里主从数据库配置就完成了，可以通过连接`192.168.2.5`，执行数据库创建操作，然后再到`192.168.2.230`看新的数据库是否创建来试验`mysql`的复制(replication)功能。

#### 4.自动创建快照
这一步开始在**从数据库**所在服务器`192.168.2.230`的数据库文件进行快照的操作，其实这一步之前，将`mysql`的数据库文件路径`/mnt1/data/mysql`挂载到了一个专用的LVM逻辑卷。  
在`192.168.2.230`执行（不进入`docker`):  
查看分区:

	root@syk230# fdisk -l
	    Device     Boot   Start        End    Sectors  Size Id Type
	/dev/sda1  *       2048     999423     997376  487M 83 Linux
	/dev/sda2       1001470 3907028991 3906027522  1.8T  5 Extended
	/dev/sda5       1001472 3907028991 3906027520  1.8T 8e Linux LVM

查看卷组:

	root@syk230# vgdisplay
	      --- Volume group ---
	  VG Name               syk230-vg
	  System ID
	  Format                lvm2
	  Metadata Areas        1
	  Metadata Sequence No  52
	  VG Access             read/write
	  VG Status             resizable
	  MAX LV                0
	  Cur LV                4
	  Open LV               3
	  Max PV                0
	  Cur PV                1
	  Act PV                1
	  VG Size               1.82 TiB
	  PE Size               4.00 MiB
	  Total PE              476809
	  Alloc PE / Size       11076 / 43.27 GiB
	  Free  PE / Size       465733 / 1.78 TiB
	  VG UUID               brRcUd-vF1G-rWjH-EJW5-jiWG-fsrN-j2RtlN

    
在`syk230-vg`卷组上创建一个名为`mysql`的逻辑卷，大小为3G:

    lvcreate -L 3G -n mysql syk230-vg
    
查看逻辑卷:

	root@syk230# lvdisplay
	  --- Logical volume ---
	  LV Path                /dev/syk230-vg/mysql
	  LV Name                mysql
	  VG Name                syk230-vg
	  LV UUID                09gVIy-r0vY-rGrX-G6sf-EDm4-483o-ZDaO66
	  LV Write Access        read/write
	  LV Creation host, time syk230, 2017-01-12 12:16:22 +0800
	  LV snapshot status     source of
	                         mysqlsnap [active]
	  LV Status              available
	  # open                 1
	  LV Size                3.00 GiB
	  Current LE             768
	  Segments               1
	  Allocation             inherit
	  Read ahead sectors     auto
	  - currently set to     256
	  Block device           252:2

格式化分区(ext4):

    mkfs.ext4 /dev/syk230-vg/mysql
    
挂载分区:

    vim /etc/fstab

添加内容:
    
    /dev/mapper/syk230--vg-mysql /mnt1/data/mysql ext4 defaults 0 2
    
至此，为存储mysql数据逻辑卷创建好了。  
**编写自动创建备份的脚本**:

	#!/bin/bash
	tmppath=/mnt1/data/mysqlbak1
	bakpath=/mnt1/data/mysqlbak
	filename=`date +%Y%m%d.tar.gz`
	binpos=/tmp/binpos.log
	
	mysql -h 172.17.0.2 -u zyl --password=zyl -e "flush tables with read lock"
	mysql -h 172.17.0.2 -u zyl --password=zyl -e "show master status\G" | grep 'File\|Position' | xargs > $binpos
	mysql -h 172.17.0.2 -u zyl --password=zyl -e "show slave status\G" | grep 'Relay_Log_File\|Relay_Log_Pos' | xargs >> $binpos
	lvcreate -s -n mysqlsnap1 -L 5G /dev/syk230-vg/mysql
	mysql -h 172.17.0.2 -u zyl --password=zyl -e "unlock tables"
	if [ ! -d "$tmppath" ]; then
	        mkdir $tmppath
	fi
	if [ ! -d "$bakpath" ]; then
	        mkdir $bakpath
	fi
	mount /dev/syk230-vg/mysqlsnap1 $tmppath
	mv $binpos $tmppath
	tar -czf "$bakpath/$filename" -C $tmppath .
	umount $tmppath
	rmdir $tmppath
	lvremove -f /dev/syk230-vg/mysqlsnap1


其中`172.17.0.2`是`docker`中`mysql_server`容器本机内网ip。可以通过

    docker network inspect bridge
    
查询。  
将执行脚本计划添加到`crontab`中:

    crontab -e

添加内容
    
    0 0 * * * /bin/bash /mnt1/data/mysqlbak.sh
    
这样每天凌晨备份一次数据库，并且不需要停止数据库。

**删除30天之前的备份**  

    find /mnt1/data/mysqlbak -mtime +30 -delete > /mnt1/data/mysqldel.sh
    chmod 755 /mnt1/data/mysqldel.sh
    
crontab 添加内容:
    
    0 0 * * * /bin/bash /mnt1/data/mysqldel.sh

## 4.小结
总的说来，配置MySQL的复制功能比配置LVM简单太多,主要是因为是否能正确的使用LVM快照功能跟硬盘配置选项有直接的关系，主要有以下几点需要注意到： 

- 安装系统的时候要选LVM管理的磁盘
- 安装系统分区的时候**一定不要将系统分区占满整个磁盘**，否则LVM无法分配新的空间来创建逻辑卷，且系统分区不可删除和缩小(否则需要用`cd-live`或者`usb-live`)
- 添加新硬盘的时候用`pvcreate`,`vgcreate`分别来创建物理卷和卷组
- 如果安装系统时没有选择LVM，用`fdisk`创建新的分区并且更改磁盘类型为`8e`(LVM类型代码)

结合MySQL的复制功能和LVM的快照功能，既能保证数据的实时同步，又能防止程序或数据库管理员的误操作。
