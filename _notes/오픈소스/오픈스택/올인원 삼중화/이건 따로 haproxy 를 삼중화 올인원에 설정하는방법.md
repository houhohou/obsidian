#openstack 
[[ubuntu 레포지터리]]
[[오픈스택]]

```bash
apt update && \\
apt install -y haproxy
```


all01
```/bin/bash
pcs resource create HAProxy systemd:haproxy op monitor interval=10s
pcs status resources

#haproxy, vip를 공동배치
pcs constraint colocation add HAProxy with vip

#vip 시작후 haproxy 시작하게 설정
pcs constraint order VirtualIP then HAProxy
```
![](https://i.imgur.com/jVAoIG1.png)


#이건그냥참고
```/bin/bash
노드이동명령어
pcs resource move HAProxy koo-controller1

```

전부 	vi /etc/haproxy/haproxy.cfg
```/bin/bash

frontend haproxy-main
        bind *:80
        default_backend alls

backend alls
        balance roundrobin
        server all01 10.185.214.251:80 check
        server all02 10.185.214.252:80 check
        server all03 10.185.214.253:80 check
```

전부 
```/bin/bash

systemctl restart haproxy
```
-설정끝!!



검증 ( 전부 all01에서 진행)


all01
```/bin/bash
pcs status


```

all01 pcs 대기상태로 변경

```/bin/bash

pcs node standby all01
pcs status
```

![](https://i.imgur.com/VQp4olR.png)


all01 pcs 대기상태 해제
```/bin/bash
pcs node unstandby all01
pcs status
```

![](https://i.imgur.com/tjRG2QZ.png)


all02 pcs 대기상태
```/bin/bash
pcs node standby all02
 pcs status
```
![](https://i.imgur.com/o9TrYlO.png)

--리소스 이동한거 확인했으면 끝!!


iptable 설정해서 
nat 60001로 들어오면 10.185.214.254:8000 으로 보냄
```/bin/bash

sudo iptables -t nat -A PREROUTING -p tcp --dport 60001 -j DNAT --to-destination 10.185.214.254:8000
sudo iptables -t nat -A POSTROUTING -j MASQUERADE
```