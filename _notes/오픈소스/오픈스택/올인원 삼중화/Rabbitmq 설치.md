#openstack 
[[ubuntu 레포지터리]]
설치, 시작

```/bin/bash
apt install -y rabbitmq-server
systemctl enable rabbitmq-server
service rabbitmq-server start
sudo rabbitmq-plugins enable rabbitmq_management
```


사용자 생성

```/bin/bash
#사용자 생성
rabbitmqctl add_user admin It1
#사용자 태그설정
rabbitmqctl set_user_tags admin tag01
#사용자 권한추가
```
유저 확인

```/bin/bash
rabbitmqctl list_users
```