#kubernetes 	

기본적인 구조

![](https://i.imgur.com/lSYAw2x.png)


조건 
	1. haproxy01은  기존사용 haproxy02는 예비용
	2. haproxy01에 장애발생시 haproxy02가 대신 받음



- ipv4.ip_forward 활성화, 설정반영  (1,2진행)
```/bin/bash
cat >> /etc/sysctl.conf EOF <<
net.ipv4.ip_forward=1 
EOF

sysctl --system
```


pacemaker & pcs설치 ( 만일 의존성 문제가 발생하면 aptitude 설치) (1,2진행)
 -
```/bin/bash
apt -y install pacemaker pcs --fix-missing

apt -y install aptitude --fix-missing
```

pacemaker, pcs 설치확인 (1,2진행)
```/bin/bash
pacemakerd --version


pcs --version
```
![](https://i.imgur.com/AulFfWt.png)




/etc/hosts파일 설정 (1,2진행)
```/bin/bash
sed -i -e "/127.0.1.1 / s/^/#/" /etc/hosts

cat >> /etc/hosts << EOF
10.185.214.211 haproxy1
10.185.214.212 haproxy2
EOF
```

pcsmaker 설치하면 생성되는 hacluster유저 패스워드 설정 (1,2진행)
```/bin/bash
passwd hacluster
```

pcsd 서비스 활성화
```/bin/bash
systemctl enable pcsd
systemctl restart pcsd
```


방화벽 오픈 (1,2진행)
```/bin/bash
ufw allow 2224/tcp
ufw allow 3121/tcp
ufw allow 5403/tcp
ufw allow 5404/udp
ufw allow 5405/udp
ufw allow 21064/tcp
```

동작중인 pcs cluser 중지, 삭제 (1,2진행)
```/bin/bash
pcs cluster stop --force
pcs cluster destroy
```


host 인증 진행 (1 진행)
```/bin/bash
pcs host auth haproxy1 haproxy2 -u hacluster -p It1
```
pcs cluster setup 진행 (1 진행)
```/bin/bash
pcs cluster setup hacluster haproxy1 haproxy2 --force
```
pcs cluster 활성화 및 실행 (1,2진행)
```/bin/bash
pcs cluster start --all
pcs cluster enable --all
```
pcs cluster 설정 진행 (1 진행)
```/bin/bash
pcs property set stonith-enabled=false
pcs property set no-quorum-policy=ignore
```
virtual ip 생성  (1 진행)
 - 여기서 가상 ip는 10.185.214.200이다, 알아서 남는 ip로 하면됨
```/bin/bash
pcs resource create vip ocf:heartbeat:IPaddr2 ip="10.185.214.200" cidr_netmask="24" op monitor timeout="30s" interval="20s" role="Slave" op monitor timeout="30s" interval="10s" role="Master"
```
ip 생성됐는지 확인 (1 진행)
```/bin/bash
ip a
```
![](https://i.imgur.com/fwnZrAa.png)

각 서비스 상태 확인 (1,2진행)
```/bin/bash
systemctl status pacemaker
systemctl status corosync
systemctl status pcsd
```

pcs 상태 확인 (1,2 진행)
```/bin/bash
pcs status
```
![](https://i.imgur.com/kqG1vc9.png)

haproxy 설치 (1,2 진행)
```/bin/bash
apt -y install haproxy --fix-missing
```
백업 진행 (1,2 진행)
```/bin/bash
cp -p /etc/haproxy/haproxy.cfg{,.bak}
```
haproxy 서비스 활성화, 실행 (1,2진행)
```/bin/bash
systemctl enable haproxy
systemctl restart haproxy
```

haproxy 서비스 등록 (1 진행)
```/bin/bash
pcs resource create haproxy systemd:haproxy configfile=/etc/haproxy/haproxy.cfg op monitor interval=10s --force
```

vip 서비스가 실행되어야 haproxy 서비스 실행되게 조건 생성 (1 진행)
```/bin/bash
pcs constraint order vip then haproxy
```

vip & haproxy resource가 동일한 node에서 실행될수 있게 설정 (1 진행)
```/bin/bash
pcs constraint colocation add vip with haproxy score=INFINITY
```
haproxy resource cleanup 진행 (1 진행)
 - cleanup을 진행하면 resource가 실행되는 node가 다운되어도 바로 다른 node에서 resource가 실행됨
```/bin/bash
pcs resource cleanup haproxy
```
pcs status 확인 (1,2 진행)
```/bin/bash
pcs status
```
![](https://i.imgur.com/txm8Y6h.png)

#### 검증

haproxy01서버 다운시키기
```/bin/bash
reboot
```
haproxy02서버에서 vip 잘 생성되는지 확인
```/bin/bash
ip a
```
![](https://i.imgur.com/sARZn2U.png)



haproxy02서버에서 상태 확인

```/bin/bash
pcs status
```
![](https://i.imgur.com/MZT5uJl.png)




