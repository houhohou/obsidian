[[ansible]]을 [[ubuntu]]에서 설치하는 방법

ubuntu 22.04

설치
```bash
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update
sudo apt-get install -y ansible
sudo apt-get install python-software-properties
```

hosts파일 설정
```bash
#ansible 호스트 설정 경로
vi /etc/ansible/hosts

10.185.214.211 #haproxy01
10.185.214.212 #haproxy02
10.185.214.221 #master01
10.185.214.222 #master02
10.185.214.223 #master03
10.185.214.226 #node01
10.185.214.227 #node02
10.185.214.228 #node03
10.185.214.213 #ingress01
10.185.214.214 #ingress02
10.185.214.231 #mgmt01
10.185.214.232 #mgmt02
10.185.214.233 #mgmt03
10.185.214.236 #storage01
10.185.214.237 #storage02
10.185.214.238 #storage03

[all]
10.185.214.211 #haproxy01
10.185.214.212 #haproxy02
10.185.214.221 #master01
10.185.214.222 #master02
10.185.214.223 #master03
10.185.214.226 #node01
10.185.214.227 #node02
10.185.214.228 #node03
10.185.214.213 #ingress01
10.185.214.214 #ingress02
10.185.214.231 #mgmt01
10.185.214.232 #mgmt02
10.185.214.233 #mgmt03
10.185.214.236 #storage01
10.185.214.237 #storage02
10.185.214.238 #storage03

[haproxy]
10.185.214.211 #haproxy01
10.185.214.212 #haproxy02

[master]
10.185.214.221 #master01
10.185.214.222 #master02
10.185.214.223 #master03

[node]
10.185.214.226 #node01
10.185.214.227 #node02
10.185.214.228 #node03

[ingress]
10.185.214.213 #ingress01
10.185.214.214 #ingress02

[mgmt]
10.185.214.231 #mgmt01
10.185.214.232 #mgmt02
10.185.214.233 #mgmt03

[storage]
10.185.214.236 #storage01
10.185.214.237 #storage02
10.185.214.238 #storage03
```