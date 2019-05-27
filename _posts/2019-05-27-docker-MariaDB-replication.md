---
layout: post
title: "MariaDB replicaion : Docker"
date: 2019-05-27 10:30:06 +0900
categories: [MariaDB, Docker]
tags: [MariaDB, MySQL, Replication, docker]
comments: true
---
# Docker 환경에서 MariaDB replicaion 구성하기

MariaDB(MySQL) replication 구성을 하려면 my.cnf 설정을 서버별로 맞춰줘야 합니다.  
성능이나 관리를 위해서 데이터 디렉토리, 로그 디렉토리들을 기본 설정이 아닌 원하는 디렉토리로 해야 할 필요도 있습니다.  
docker 환경에서 MariaDB Replication 구성을 좀 더 쉽게 하기 위한 my.cnf와 shell 스크립트를 만들어 봤습니다.

## my.cnf
데이터는 /MariaDB/data 디렉토리를 사용하고 로그는 /MariaDB/log 디렉토리를 사용합니다.  
replication을 위해서 binlog와 relaylog 디렉토리들은 /MariaDB/log 디렉토리 하위로 사용합니다.  

```
####
#### Ohnew Innovation
#### MariaDB my.cnf
####

[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
socket		= /var/run/mysqld/mysqld.sock
nice		= 0
# syslog
#skip_log_error
#syslog

[mysqld]
# basic setting
#user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /MariaDB/data
tmpdir		= /tmp
lc_messages_dir	= /usr/share/mysql
lc_messages	= en_US
skip-external-locking
bind-address=0.0.0.0

skip-host-cache
skip-name-resolve

#server-id=2179

#query_cache setting
query_cache_type    = 0
query_cache_size    = 0
query_cache_limit   = 0

# Log file setting
log-error           = /MariaDB/log/mariadb.err
slow_query_log      = 1
slow_query_log_file = /MariaDB/log/slow-query.log
long_query_time     = 3
#log-queries-not-using-indexes
#log_slow_admin_statements

log-bin             = /MariaDB/log/binary/mariadb-bin
binlog_cache_size   = 2M
max_binlog_size     = 100M
expire_logs_days    = 10
sync_binlog		    = 1
binlog_format       =row

# slaves
relay_log           = /MariaDB/log/binary/relay-bin
#log_slave_updates
#read_only

# Time-out setting
wait_timeout         = 600
interactive_timeout  = 3600
connect_timeout		 = 5

# transaction isolation setting
transaction_isolation = READ-COMMITTED

# etc setting
back_log            = 1024
max_connections     = 1024
max_connect_errors  = 1024
table_open_cache    = 2048
max_allowed_packet  = 1024M
max_heap_table_size = 1024M
sort_buffer_size    = 8M
join_buffer_size    = 8M
thread_cache_size   = 128
#thread_concurrency = 36
tmp_table_size      = 1024M

# character set setting
#init_connect = "SET collation_connection = utf8_general_ci"
#init_connect = "SET NAMES utf8"
character-set-server = utf8mb4
#collation-server     = utf8mb4_general_ci 
#character_set_server = utf8mb4 
#collation_server     = utf8mb4_general_ci

# innodb setting
innodb_file_per_table          = 1
innodb_buffer_pool_size        = 512M
innodb_log_buffer_size         = 16M
innodb_lock_wait_timeout       = 50
innodb_thread_sleep_delay      = 0
innodb_commit_concurrency      = 36
innodb_doublewrite             = 0
innodb_flush_log_at_trx_commit = 2
innodb_thread_concurrency      = 36
#innodb_log_files_in_group      = 3
innodb_flush_method	           = O_DIRECT
innodb_log_file_size           = 256M
#innodb_data_file_path    = ibdata1:1024M;ibdata2:1024M;ibdata3:10M:autoextend

# use MyISAM
key_buffer_size          = 128M
read_buffer_size         = 2M
read_rnd_buffer_size     = 16M
bulk_insert_buffer_size  = 64M
myisam_sort_buffer_size  = 512M
myisam_max_sort_file_size= 512M
myisam_repair_threads    = 1
myisam_recover

# Galera
#wsrep_on=ON
#wsrep_provider=
#wsrep_cluster_address=
#default_storage_engine=InnoDB
#innodb_autoinc_lock_mode=2
#wsrep_slave_threads=1

[mysqldump]
max_allowed_packet	= 1024M
quick
#quote-names

[mysqld_safe]
open_files_limit = 8192
```


## maria-create.sh
각 container 들의 cnt, data, log 디렉토리를 생성하고 my.cnf에서 server-id 설정을 변경합니다.  
4개의 docker container를 생성합니다. (maria1 ~ maria4)  
maria3 서버를 replication master 서버로 사용합니다.  
master 서버에 slave 서버들이 replication slave로 사용할 repl 계정을 만듭니다.  
GTID 사용해서 replication 연결을 하기 위한 master position을 GTIDpos 변수에 설정합니다.  
master 서버 이외의 container들을 change master 명령으로 slave 서버 설정을 합니다.  

```
#!/bin/bash
####
#### Ohnew Innovation
#### MariaDB Replication : Docker
####


Nodes=4 
NodeMaster=3
MyPass="ohnew21&("

for i in `seq 1 $Nodes`; do 
    mkdir -p $PWD/conf/node$i $PWD/data/node$i $PWD/log/node$i/binary
    cp my.cnf $PWD/conf/node$i
    sed -i "s/#server-id=2179/server-id=$i/g" conf/node$i/my.cnf
done

chown -R 999:999 ./data/*
chown -R 999:999 ./log/*

for i in `seq 1 $Nodes`; do 
    docker run -d -it \
    --network=maria --name=maria$i --memory-swappiness=1 \
    --mount type=bind,src=$PWD/conf/node$i,dst=/etc/mysql \
    --mount type=bind,src=$PWD/data/node$i,dst=/MariaDB/data \
    --mount type=bind,src=$PWD/log/node$i,dst=/MariaDB/log \
    -e MYSQL_ROOT_PASSWORD="$MyPass" \
    mariadb
done

echo -ne "maria$NodeMaster "
while true; do
  chkPing=`docker container exec maria$NodeMaster mysqladmin -s -p"$MyPass" ping > chk.ping`
  if grep "mysqld is alive" chk.ping ; then
    break
  fi
  echo -ne "."
  sleep 1
done

docker container exec maria$NodeMaster \
    mysql -p"$MyPass" -e \
      "CREATE USER 'repl'@'%' IDENTIFIED BY '$MyPass'; GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';"

GTIDpos=`docker container exec maria$NodeMaster mysql -s -p"ohnew21&(" -e "select @@global.gtid_binlog_pos"`

for i in `seq 1 $Nodes`; do 
  if [ "$i" -eq "$NodeMaster" ] ; then
    echo -ne ""
  else

    echo -ne "maria$i "
    while true; do
      chkPing=`docker container exec maria$i mysqladmin -s -p"$MyPass" ping > chk.ping`
      if grep "mysqld is alive" chk.ping ; then
        break
      fi
      echo -ne "."
      sleep 1
    done

    docker container exec maria$i \
      mysql -hmaria$i -p"$MyPass" -e \
      "SET GLOBAL read_only = 1; SET GLOBAL gtid_slave_pos = '$GTIDpos'; CHANGE MASTER TO MASTER_HOST='maria$NodeMaster', MASTER_USER='repl', MASTER_PASSWORD='$MyPass', MASTER_USE_GTID = current_pos; START SLAVE;"
    
    NodeSlave=$i

  fi
done

docker container exec maria$NodeSlave mysql -s -p"ohnew21&(" -e "show slave status\G" | grep Running:
```

## 스크립트 실행
my.cnf와 maria-create.sh 파일을 동일한 디렉토리에 만들고 maria-create.sh 파일을 실행하면 4개의 container가 만들어지고, 해당 디렉토리 밑으로 conf, data, log 디렉토리들에 node1, node2, node3, node4 서버별로 관련 파일들이 생깁니다.

docker에서는 galera cluster를 사용하는게 replication 설정보다 구성하기가 좋을거 같습니다. MaxScale이나 ProxySQL같은 DB Proxy를 어떻게 사용할것인지에 따라서 장단점은 있겠습니다.


