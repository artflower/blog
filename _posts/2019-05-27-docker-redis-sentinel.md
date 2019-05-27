---
layout: post
title: "Redis Sentinel : Docker"
date: 2019-05-27 11:30:06 +0900
categories: [Redis, Docker]
tags: [Redis, replication, sentinel, docker]
comments: true
---
# Docker 환경에서 Redis Sentinel replicaion 구성하기
Redis Sentinel 구성을 위해서 4개의 container를 사용합니다.  
- redis-snt-1 : redis master  
- redis-snt-2,3,4 : redis slave, sentinel  

sentinel 구성을 위해서 아래 파일들을 사용합니다.  
- redis.conf : redis 환경 설정 파일  
- sentinel.conf : sentinel 환경 설정 파일  
- redis-start.sh : docker 실행 스크립트  

## redis.conf
일반적인 redis.conf 파일을 사용합니다.  
```
# port 7901

dir "/data"
logfile "redis.log"

maxclients 1128
maxmemory 1000000000
maxmemory-policy volatile-lru

appendonly yes
appendfsync everysec
repl-backlog-size 50mb
```

## sentinel.conf
```
port 8179
sentinel monitor rsmaster redis-snt-1 6379 1
sentinel down-after-milliseconds rsmaster 3000

bind 0.0.0.0
protected-mode yes
dir "/data/sentinel"
logfile "sentinel.log"
daemonize yes
```

## redis-start.sh
```
#!/bin/sh
set -e

[ -d /data/sentinel ] || mkdir -p /data/sentinel && chown -R redis:redis /data

gosu redis redis-sentinel /usr/local/etc/redis/sentinel.conf
gosu redis redis-server /usr/local/etc/redis/redis.conf --slaveof redis-snt-1 6379
```

## redis sentinel 구성
현재 디렉토리 밑으로 docker 별로 사용할 conf, sh 파일을 복사합니다.  
redis master로 사용할 서버에 대한 설정을 변경합니다.  
docker run을 실행하면서 sentinel을 같이 실행하기 위해서 start.sh 실행으로 시작합니다.
```
for ind in `seq 1 4`; do \
 mkdir -p $PWD/data/conf/node$ind
 cp redis-master.conf data/conf/node$ind/redis.conf
 cp sentinel.conf data/conf/node$ind/sentinel.conf
 cp redis-start.sh data/conf/node$ind/start.sh
done

# chown 999:999 data/conf/node*/*.conf
# sed -e 's/gosu redis redis-sentinel/#gosu redis redis-sentinel/' redis-start.sh > data/conf/node1/start.sh
# sed -e 's/--slaveof redis-snt-1 6379//' redis-start.sh > data/conf/node1/start.sh

### docker run
for ind in `seq 1 4`; do \
 docker run -d \
 -v $PWD/data/conf/node$ind:/usr/local/etc/redis \
 -v $PWD/data/node$ind:/data \
 --name redis-snt-$ind --net redis-cluster \
 redis /usr/local/etc/redis/start.sh
done
```

docker에서 redis cluster 이용하는게 좋겠으나 특정 서비스에서 sentinel 사용을 요구할때 최소의 container로 sentinel 구성을 어떻게 하면 좋을까 고민하다가 하나의 container에 redis 서버와 sentinel을 같이 실행할 수 있겠다 싶었습니다.  
