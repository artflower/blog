---
layout: post
title: "Percona - MySQL performance : kubernetes"
date: 2019-05-13 13:00:06 +0900
categories: [Percona, Kubernetes]
tags: [Percona, Kubernetes, MySQL, k8s, docker]
comments: true
---
# Percona - MySQL performance : kubernetes

Percona 블로그에 Kubernetes 환경에서 MySQL 성능 관련한 글이 올라 왔습니다.
https://www.percona.com/blog/2019/05/09/measuring-mysql-performance-kubernetes/

기본적인 배포로는 CPU의 성능을 제대로 사용하지 못해서 50% 이하로 사용을 한다고 합니다.  
YAML에서 아래와 같이 CPU, memory 설정을 해주면 K8S 없는 일반적인 서버의 90% 성능으로 사용을 할 수 있다고 합니다. 90% 수준의 성능은 K8S 사용을 감안하면 괜찮습니다.  

~~~
resources:
    requests:
        cpu: "55500m"
        memory: "150Gi"
    limits:
        cpu: "55500m"
        memory: "150Gi"
~~~

실제 환경에서는 여러 상황들을 고려해서 성능 측정을 하고 적절한 설정값을 찾아야 겠습니다.
