#openstack 
[[ubuntu 레포지터리]]
### 기능 : 고가용성 클러스터 리소스 관리자 소프트웨어 
 - 여러대의 서버를 클러스터로 그룹화 해서 시스템 가용성, 안정성을 높이기 위해 리소스 관리하고 자동장애 복구 서비스 이용등을 관리
 - 



pcs: pacemaker를 쉽게 사용할수 있도록 돕는 cli도구



3. PCS vip와 HAProxy 구성
- haproxy 서비스 활성화 및 실행 (haproxy 서버에서 진행)


pcs 서버 

각각 
all01 #10.185.214.251
all02 #10.185.214.252
all03 #10.185.214.253
vip 10.185.214.254
이렇게구성할거다

```/bin/bash

cat >> /etc/sysctl.conf << E0F
net.ipv4.ip_forward=1
E0F

sysctl --system


apt -y install pacemaker pcs


apt -y install aptitude
```

```/bin/bash

pacemakerd --version
corosync -v
pcs --version
```

```/bin/bash

sed -i -e "/127.0.1.1 / s/^/#/" /etc/hosts



## haproxy 서버 정보 등록
cat >> /etc/hosts << E0F 

10.185.214.251 all01
10.185.214.252 all02
10.185.214.253 all03
E0F
```

```/bin/bash
passwd hacluster

```
```/bin/bash
systemctl enable pcsd
systemctl restart pcsd
ufw allow 2224/tcp
ufw allow 3121/tcp
ufw allow 5403/tcp
ufw allow 5404/udp
ufw allow 5405/udp
ufw allow 21064/tcp
pcs cluster stop --force
pcs cluster destroy
```

all01서버만
```/bin/bash
pcs host auth all01 all02 all03 -u hacluster -p It1
pcs cluster setup hacluster all01 all02 all03 --force
```

```/bin/bash
pcs cluster start --all
pcs cluster enable --all
```

all01서버만
```/bin/bash
pcs property set stonith-enabled=false
pcs property set no-quorum-policy=ignore
```


all01서버만

```/bin/bash
pcs resource create vip ocf:heartbeat:IPaddr2 ip="10.185.214.254" cidr_netmask="24" op monitor timeout="30s" interval="20s" role="Slave" op monitor timeout="30s" interval="10s" role="Master"
```


IP확인해보면 두개 달려있음

![](https://i.imgur.com/Ni50JmA.png)


all01 셧다운되면
![](https://i.imgur.com/Mpe1heb.png)

all01, all02셧다운되면
![](https://i.imgur.com/AWk9uaU.png)


pacemaker 이해하는데 참고되는 사이트
https://tech.osci.kr/%EA%B8%B0%EC%B4%88%EB%B6%80%ED%84%B0-%EC%8B%9C%EC%9E%91%ED%95%98%EB%8A%94-linux-pacemaker%EC%9D%98-%EC%9D%B4%ED%95%B4/






