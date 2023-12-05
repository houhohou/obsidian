
[[리눅스]]에서 로드밸런싱을 하기위해 사용하는 오픈소스

사용 예
```bash
vi /etc/haproxy/haproxy.cfg

#--------------------------
#       apt경로 지정
#--------------------------
frontend apt_frontend
        bind 10.0.0.1:80
        mode http
        default_backend apt_backend

backend apt_backend
        mode http
        balance roundrobin
        server repo 10.0.0.2:80 check

#--------------------------
#        넥서스 경로 지정
#--------------------------
frontend nexus_frontend
         bind 10.0.0.101:5000
         mode http
         default_backend nexus_backend
backend nexus_backend
        mode http
        balance roundrobin
        server repo 10.0.0.102:5000 check
#--------------------------
#       listen 경로 지정
#--------------------------
listen ingress
        bind *:443
        mode tcp
        option tcplog
        server ingress 10.0.0.201:31963
```

```bash
global
        #log /dev/log    local0
        #log /dev/log    local1 notice
        log 127.0.0.1 localhost
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon
        maxconn 40000000 #최대연결갯수 
```
haproxy의 구성 파일 섹션

global : all 같은 역할로 전체 영역에 적용되는 기본설정을 담당
default : frontend, backend, listen에 영향
frontend : 클라이언트에서 오는 요청을 받는곳
backend : 로드밸런스 단계로 client에서 들어온 트래픽을  뒷단서버로 처리
listen : front와 backend로 사용되는 포트를 한번에 설정하는 영역으로 tcp프록시에서만 사용


![](https://i.imgur.com/dtawo1h.png)

위 그림을 보면 클라이언트에서 오는 요청이 frontend
	뒷단으로 로드밸런싱 하는 역할이 backend



Roundrobin : 순차적으로 분배
static-rr : 서버에 부여된 가중치에 따라서 분배
leastconn : 접속수가 가장 적은 서버로 분배
source : 운영중인 서버의 가중치를 나눠서 접속자 IP 해싱(hashing)해서 분배
uri : 접속하는 URI를 해싱해서 운영중인 서버의 가중치를 나눠서 분배 (URI의 길이 또는 depth로 해싱)
url_param : HTTP GET 요청에 대해서 특정 패턴이 있는지 여부 확인 후 조건에 맞는 서버로 분배 (조건 없는 경우 round_robin으로 처리)
hdr : HTTP 헤더에서 hdr(<name>)으로 지정된 조건이 있는 경우에 대해서만 분배 (조건없는 경우 round robin 으로 처리)
rdp-cookie : TCP 요청에 대한 RDP 쿠키에 따른 분배


참고 블로그 
https://lascrea.tistory.com/212


