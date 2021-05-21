---
layout: post
title: "WSL2 환경에서 vagrant를 이용한 테스트 환경 만들기"
date: 2021-05-25 17:30:06 +0900
categories: [linux]
tags: [wsl, vagrant]
comments: true
---
# WSL2 환경에서 vagrant를 이용한 테스트 환경 만들기
윈도우10에서 WSL을 사용해서 linux를 사용할 수 있습니다.
linux 테스트 환경을 만들기 위해서 vagrant를 이용하면 쉽게 구성을 할 수 있습니다.
WSL2 환경에 vagrant를 이용해서 centos 테스트 환경을 만들어서 사용하면 쉽게 linux 관련 테스트를 할 수 있습니다.

WSL2 설치는 여러 설치 관련 문서들이 있으니 참고하시면 되고요.

## vagrant 설치
WSL2에 vagrant 설치는 아래 웹페이지를 참고 하면 됩니다.
https://blog.thenets.org/how-to-run-vagrant-on-wsl-2/

주요 설치 관련 명령은 아래와 같습니다.
```
# run inside WSL 2
# check https://www.vagrantup.com/downloads for more info
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant

# append those two lines into ~/.bashrc
echo 'export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"' >> ~/.bashrc
echo 'export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"' >> ~/.bashrc

# now reload the ~/.bashrc file
source ~/.bashrc
```

## vagrant 설정
2대의 centos VM을 만드는 Vagrantfile은 아래와 같습니다.
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

#Vagrant_API_Version = "2"
Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.box_check_update = false
  #test01
  config.vm.define:"neoclova01" do |cfg|
    cfg.vm.provider:virtualbox do |vb|
      vb.name="CentOS-neoclova01"
      vb.customize ["modifyvm", :id, "--cpus",2]
      vb.customize ["modifyvm", :id, "--memory",1024]
    end
    cfg.vm.host_name="neoclova01"
    cfg.vm.synced_folder ".", "/vagrant", disabled:true
#    cfg.vm.network "public_network", ip: "1.1.1.1"
    cfg.vm.network "private_network", ip: "192.168.200.101"
#    cfg.vm.network "forwarded_port", guest: 22, host:39211, auto_correct: false, id: "ssh"
#    cfg.vm.provision "shell", inline: <<-SHELL
#       echo "hello"
#       date
#    SHELL
  end
  #test02
  config.vm.define:"neoclova02" do |cfg|
    cfg.vm.provider:virtualbox do |vb|
      vb.name="CentOS-neoclova02"
      vb.customize ["modifyvm", :id, "--cpus",2]
      vb.customize ["modifyvm", :id, "--memory",1024]
    end
    cfg.vm.host_name="neoclova02"
    cfg.vm.synced_folder ".", "/vagrant", disabled:true
#    cfg.vm.network "public_network", ip: "8.8.8.8"
    cfg.vm.network "private_network", ip: "192.168.200.102"
#    cfg.vm.network "forwarded_port", guest: 22, host:39212, auto_correct: false, id: "ssh"
  end
end
```
## vagrant 실행
WSL2에서 vagrant ssh를 사용하기 위해서는 WSL2 플러그인을 설치해야 합니다.
```
vagrant plugin install virtualbox_WSL2

vagrant up
```

