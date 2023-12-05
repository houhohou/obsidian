#kubernetes 
[[kubernetes]]의 Priviate 환경에서 DNS 사용하게 하는 
먼저 [[perconaXtradb]] 설치해서 이중화든 삼중화든해놓자.


db접속 ( 연동된 percona 아무곳이나 한곳에서만)
```/bin/bash
mysql -u root -pcloud1234
```

powerdns db, user, 권한 설정
```mysql
create database powerdns;

create user 'powerdns'@'localhost' identified by 'cloud1234';
create user 'powerdns'@'%' identified by 'cloud1234';
grant all privileges on powerdns.* to 'powerdns'@'localhost';
grant all privileges on powerdns.* to 'powerdns'@'%';
flush privileges;
exit
```

 resolv 서비스 종료( db 서버 전체 )
```/bin/bash
systemctl disable --now systemd-resolved

rm /etc/resolv.conf



vi /etc/resolv.conf
---
nameserver 10.185.214.230
nameserver 10.185.214.231
---
```


