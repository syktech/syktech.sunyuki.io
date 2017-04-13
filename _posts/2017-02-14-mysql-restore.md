---
layout: post
title: MySQL 数据库恢复
tags: mysql
author: zengyilun
---
## 二进制日志的恢复

二进制日志非常关键，用户可以通过它完成`point-in-time`的恢复工作。MySQL数据库的replication同样需要二进制日志。  
<!-- more -->

要恢复二进制日志是非常简单的，通过mysqlbinlog即可。mysqlbinlog的使用方法如下：

    shell>mysqlbinlog [options] logfile

例如要还原binlog.000001，可以使用以下命令：

    shell>mysqlbinlog binlog.000001 | mysql -h 171.17.0.2 -u root --password=pwd

也可以先导出到一个sql文件，再用source命令来导入
 
    shell>mysqlbinlog binlog.000001 > /tmp/dump.sql
    shell>mysql -h 171.17.0.2 -u root --password=pwd -e "source /tmp/dump.sql"

用`--start-position`和`--stop-position`选项可以选择特定位置的二进制日志。

## 恢复场景

server1 `192.168.2.5` docker容器运行 mysql **主数据库**  
server2 `192.168.2.230` docker容器运行 mysql **从数据库**

以下以`2.5`代称`192.168.2.5`,`2.230`代称`192.168.2.230`。

主从关系正常，每次主数据库的操作都会映射到从数据库，
且 从数据库的系统变量 `log_slave_updates=TRUE`（启用slave replication日志写入binlog）。

`2.230` 的 mysql 数据文件 在 `/mnt1/data/mysql` 文件夹。

`2.230` mysql 数据库每天使用 `lvm` 备份了一份 `mysql` 数据文件 如 `/mnt1/data/mysqlbak/20170214.tar.gz`。

### 场景1 `2.5`误删数据同步到了`2.230`

昨天`2017-02-13 14:00:00` 数据库管理人员在`2.5`创建了数据库`test1` 而且写入了一些数据，`2.230`同步创建。

昨天`2017-02-13 23:00:00` 备份了一份数据文件`/mnt1/data/mysqlbak/20170213.tar.gz`，备份的时候，锁表后，通过`show master  status`已记录下最新`binlog`的`position`，解锁，存于`02170213.tar.gz/binpos.log`。

今天`2017-02-14 14:00:00`，由于数据库管理人员的失误，在`2.5` `test1`数据库删除了，`2.230`也同步了删除，这并不是数据库管理人员期望的结果，它希望在`2.230`上恢复`test1`数据库。

思路：将备份的数据文件完全覆盖现在的数据文件，然后通过恢复后继二进制日志来达到恢复的目的，备份时的二进制日志position作为start position到需要跳过的语句的posistion作为stop position。跳过后的语句作为start postion到二进制日志末尾。

创建临时文件夹

    root@192.168.2.230$ cd /mnt1/data/
    root@192.168.2.230$ mkdir -p tmpres/bak
    root@192.168.2.230$ mkdir -p tmpres/old
    
解压备份文件

    root@192.168.2.230$ tar -xzf /mnt1/data/mysqlbak/20170213.tar.gz -C /mnt1/data/

拷贝现在的数据文件

    root@192.168.2.230$ cp -r /mnt1/data/mysql/* /mnt1/data/tmpres/old

到此为止，数据库备份数据文件在 `/mnt1/data/tmpres/bak`， 数据库现在的数据文件在 `/mnt1/data/tmpres/old`。  

用备份数据覆盖现在的数据库数据

    root@192.168.2.230$ rm -rf /mnt1/data/mysql/*
    root@192.168.2.230$ cp -r /mnt1/data/tmpres/bak/* /mnt1/data/mysql

查看备份时的binlog position

    root@192.168.2.230$ cat /mnt1/data/tmpres/bak/binpos.log
    File: mysql-bin.000026 Position: 763
    Relay_Log_File: mysql-relay-bin.000026 Relay_Log_Pos: 837

可以看到当时的binlog start position为763，现在只需要找出binlog stop postion，即需要略过的删数据库postion。

找出需要跳过的语句的position

    root@192.168.2.230$ mysqlbinlog /mnt1/data/mysql/old/mysql-bin.000026
    /*!*/;
    # at 895
    #170214 22:34:09 server id 1  end_log_pos 852 CRC32 0x4266be1d  Query   thread_id=319   exec_time=4294932166    error_code=0
    SET TIMESTAMP=1487082849/*!*/;
    DROP DATABASE `test1`
    /*!*/;
    # at 905
    ...
    # at 1052
    #170214 12:49:30 server id 2  end_log_pos 899 CRC32 0x833b69af  Rotate to mysql-bin.000027  pos: 4
    SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
    DELIMITER ;
    # End of log file
    /*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
    /*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;

找到了drop database 的 postion 为895，所以start-postion=763,stop-position=895。下一段binlog的start-position=905,end-position=EOF 文件末尾。

    root@192.168.2.230$ mysqlbinlog --start-position=763 --stop-position=895 | mysql -h 171.17.0.2 -u root --password=pwd
    root@192.168.2.230$ mysqlbinlog --startposition=905 | mysql -h 171.17.0.2 -u root --password=pwd

从数据库已经恢复完成。

### 场景2 主库备库切换

当主库出现问题时，这时需要立即切换到备库，主要流程如下：

1.  限制主库访问，只有同步备库的角色才能访问。
2.  确认备库是否同步完成并关闭replication功能。
3.  关掉主库，程序正式使用备库。

#### 1.主库设置限制访问

锁表，只能读，不能写：
    
    mysql> flush tables with read lock;
    
#### 2.确认备库是否同步完成并关闭同步功能

    mysql> show slave status;
    mysql> stop slave;
    
如果`Slave_IO_State` 是 `Waiting for master to send event`， 说明已同步完成。

#### 3.关闭主库
    
    root@192.168.2.5$ service mysql stop
    
关闭主库后，程序的连接设置成备库。
