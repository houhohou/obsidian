[[ubuntu]] 에 [[repo]] 경로 지정해주는거

구상은이런데 

원리는 haproxy의 모든 60008번 포트로 오는 요청을 101.202.40.5:80으로 보내는 원리이다. 
listen이 frontend, backend를 다 설정하기에, 받고 주는 것을한번에 설정함

server01, server02, server03에서 apt repo를 수정해서 apt요청을 haproxy:60008로 요청을 보냄

![](https://i.imgur.com/Vevg6hl.png)



haproxy
사내용 network, 외부통신가능한 network두개가 필요

server01, server02, server03
사내용 ip만 필요


server01,server02,server03 전부

```bash
sed -i 's/deb/deb [arch=amd64]/g' /etc/apt/sources.list
sed -i 's/archive\.ubuntu\.com/10.0.0.11:60008/g' /etc/apt/sources.list
sed -i 's/focal/jammy/g' /etc/apt/sources.list
sed -i 's|ubuntu|hana-ubuntu/mirror/kr.archive.ubuntu.com/ubuntu|g' /etc/apt/sources.list
```

haproxy서버 
```bash
#haproxy 설치
apt install -y haproxy

#haproxy cfg설정
cat >> /etc/haproxy/haproxy.cfg << E0F
listen repo
        bind 0.0.0.0:60008
        balance  source
        option  tcpka
        #option  httpchk
        option  tcplog
        server registry 101.202.40.5:80 check inter 2000 rise 2 fall 5
E0F
#haproxy재시작
systemctl restart haproxy
```

server01,server02,server03 전부
```bash
#레포지터리 업데이트
apt update -y
apt upgrade -y
```
