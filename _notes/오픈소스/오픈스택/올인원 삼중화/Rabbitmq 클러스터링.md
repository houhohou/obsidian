#openstack 
[[ubuntu 레포지터리]]
[[Rabbitmq 설치]]
기본베이스 

all01 10.185.214.251
all02 10.185.214.252
all03 10.185.214.253






전체 서버 rabbitmq 설치

[[Rabbitmq 설치]]

```/bin/bash
cat >> /etc/hosts << E0F
10.185.214.251 all01
10.185.214.252 all02
10.185.214.253 all03
E0F

reboot
```

#### Erlang 쿠키 맞추기(Erlang cookie가 다르면 통신이 안돼서 클러스터링 안됨)


all02, all03 서버

```/bin/bash

sudo service rabbitmq-server stop

cp -p /var/lib/rabbitmq/.erlang.cookie /var/lib/rabbitmq/.erlang.cookie.bak

sudo ufw allow 25672
sudo ufw allow 4369
```




all01 서버

```/bin/bash

#백업
cp -p /var/lib/rabbitmq/.erlang.cookie /var/lib/rabbitmq/.erlang.cookie.bak

#방화벽오픈
sudo ufw allow 25672
sudo ufw allow 4369

#erlang 값 전송
scp /var/lib/rabbitmq/.erlang.cookie root@all02:/var/lib/rabbitmq/.erlang.cookie

scp /var/lib/rabbitmq/.erlang.cookie root@all03:/var/lib/rabbitmq/.erlang.cookie
sudo service rabbitmq-server start

```
all02, all03 서버

```/bin/bash
#서버 잘 올라오나 확인
sudo service rabbitmq-server start
sudo rabbitmqctl cluster_status

#클러스터링 진행
sudo rabbitmqctl stop_app
rabbitmqctl join_cluster rabbit@all01
sudo rabbitmqctl start_app

#클러스터링 후 서버 확인
sudo rabbitmqctl cluster_status


```

![](https://i.imgur.com/GtGZT9y.png)




참고블로그
https://visionboy.me/790


scp /var/lib/rabbitmq/.erlang.cookie root@10.185.214.252:/var/lib/rabbitmq/ && \
scp /var/lib/rabbitmq/.erlang.cookie root@10.185.214.253:/var/lib/rabbitmq/

rabbitmqctl join_cluster rabbit@all01 --ram