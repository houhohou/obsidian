#kubernetes 
[[kubernetes]]의 이미지 저장소
우분투 22.04
최소 cpu 4


Nexus repo management 
 - 내부망 서버들이 인터넷과 연결된 nexus 서버를 통해 외부에 repository를 통해 패키지나 image를 다운로드 받을 수 있다.  
 - nexus 서버에는 해당 패키지나 image들이 저장되어 외부가 아닌 nexus 서버에 존재하는 패키지나 image를 통해 다운로드 가능


필요 구성 파일 다운
```/bin/bash
apt -y install openjdk-8-jdk --fix-missing
```

넥서스 다운로드 & 압축풀기
```/bin/bash
wget https://download.sonatype.com/nexus/3/nexus-3.58.1-02-unix.tar.gz
tar -zxf nexus-3.58.1-02-unix.tar.gz -C /opt/
```


압축해제되면 디렉터리 2개 확인
```/bin/bash
ll /opt/
```

포트 허용
```/bin/bash
ufw allow 8081/tcp
```

포그라운드로 실행
```/bin/bash
/opt/nexus-3.58.1-02/bin/nexus run
```
아래처럼 나오면 뜬다 ( root로 접속하면 경고뜨는데, 무시)
![](https://i.imgur.com/4NuFdV4.png)


접속은 아래 주소로 하면 됨

http://192.168.34.45:8081


백그라운드로 실행
```/bin/bash
# nexus 실행
/opt/nexus-3.58.1-02/bin/nexus start

# nexus 재실행
/opt/nexus-3.58.1-02/bin/nexus restart

# nexus 종료
/opt/nexus-3.58.1-02/bin/nexus stop
```

백그라운드로 실행될때 로그 확인


```/bin/bash
tail -f /opt/sonatype-work/nexus3/log/nexus.log
```
![](https://i.imgur.com/uD3TUlI.png)


nexus 사용자와 그룹 생성 후 디렉터리 소유권 변경
```/bin/bash
useradd -d /opt/nexus-3.58.1-02 -s /bin/bash nexus
chown -R nexus:nexus /opt/nexus-3.58.1-02 /opt/sonatype-work
```


런타임 실행유저를 nexus 유저로 지정
```/bin/bash
cat >> /opt/nexus-3.58.1-02/bin/nexus.rc << E0F

run_as_user="nexus"

E0F
```



( 옵션 ) JVM 관련 메모리 변경
```/bin/bash
vi /opt/nexus-3.58.1-02/bin/nexus.vmoptions
```
![](https://i.imgur.com/rxBAndp.png)

( 옵션 ) nexus 접속 포트번호와 허용 ip 지정가능

```/bin/bash
cat >> /opt/sonatype-work/nexus3/etc/nexus.properties << E0F
application-port=8081 
application-host=0.0.0.0
E0F
```

systemd로 nexus관리하게 .service 파일 만들기

```/bin/bash
cat >> /etc/systemd/system/nexus.service << E0F

[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus-3.58.1-02/bin/nexus start
ExecStop=/opt/nexus-3.58.1-02/bin/nexus stop
User=nexus
Restart=on-abort
TimeoutSec=600

[Install] 
WantedBy=multi-user.target

E0F
```

nexus 서비스 실행 및 확인
```/bin/bash
systemctl daemon-reload
systemctl enable nexus.service
systemctl start nexus.service
systemctl status nexus.service
```


초기 패스워드 확인 ( 아이디 admin )
```/bin/bash
cat /opt/sonatype-work/nexus3/admin.password
```
![](https://i.imgur.com/BCpd4C6.png)


## nexus백업관한 설정
작업내용
 - Blob저장소
 - 다른 서버로 백업 및복원하는 경우에 필요한 고유 node id
 - blob 저장소를 가리키는 포인터인 db

디렉터리 생성 및 소유권 변경
```/bin/bash
mkdir -p /opt/nexus-backup/{db,id,blob} 
chown -R nexus:nexus /opt/nexus-backup
```

Create task 클릭

![](https://i.imgur.com/DjHeQkE.png)


Admin -Export databases for backup

![](https://i.imgur.com/lpsXUkM.png)

아래처럼 설정

![](https://i.imgur.com/zcDWDI0.png)

Run 시작

![](https://i.imgur.com/u7hPqKK.png)

Last result가 Ok인지 확인

![](https://i.imgur.com/Dx1bHJz.png)


잘 백업됐는가 확인
```/bin/bash
ll /opt/nexus-backup/db/
```
![](https://i.imgur.com/lqxi2vU.png)



nexus 종료
```/bin/bash
systemctl stop nexus.service
```

고유한 node id가 있는 디렉터리 확인
```/bin/bash
ll /opt/sonatype-work/nexus3/keystores/node/
```
![](https://i.imgur.com/MWw5GxF.png)


id 백업 후 확인
```/bin/bash
cp -p /opt/sonatype-work/nexus3/keystores/node/* /opt/nexus-backup/id/
ll /opt/nexus-backup/id/
```
![](https://i.imgur.com/q7AVHwT.png)



```/bin/bash
cp -rp /opt/sonatype-work/nexus3/blobs /opt/nexus-backup/blob/
ll /opt/nexus-backup/blob/
```
![](https://i.imgur.com/tLIlka8.png)



```/bin/bash
mv /opt/nexus-backup .
tar -zcf nexus-backup.tar.gz nexus-backup
rm -rf nexus-backup
ll nexus-backup.tar.gz
```
![](https://i.imgur.com/W98dVGd.png)


새 nexus 생성을 위한 기존 nexus 지우기
```/bin/bash
rm -rf /opt/nexus-3.58.1-02 /opt/sonatype-work
ll nexus-3.58.1-02-unix.tar.gz
```
![](https://i.imgur.com/klLa92w.png)


압축풀기
```/bin/bash
tar -zxf nexus-3.58.1-02-unix.tar.gz -C /opt/
chown -R nexus:nexus /opt/nexus-3.58.1-02 /opt/sonatype-work
systemctl restart nexus.service
```
동작시켜서 생긴 db 디렉터리에 component, config, security 디렉터리 삭제
```/bin/bash
rm -rf /opt/sonatype-work/nexus3/db/component/
rm -rf /opt/sonatype-work/nexus3/db/config/
rm -rf /opt/sonatype-work/nexus3/db/security/
```

압축했던 nexus 파일을 압축해제하고 다시 복원
```/bin/bash
tar -zxf nexus-backup.tar.gz
cp -p nexus-backup/db/* /opt/sonatype-work/nexus3/restore-from-backup/
cp -p nexus-backup/id/* /opt/sonatype-work/nexus3/keystores/node/
cp -rp nexus-backup/blob/blobs /opt/sonatype-work/nexus3/
rm -rf nexus-backup
chown -R nexus:nexus /opt/nexus-3.58.1-02 /opt/sonatype-work
```

런타임 실행유저 설정
```/bin/bash
cat >> /opt/nexus-3.58.1-02/bin/nexus.rc << E0F

run_as_user="nexus"

E0F
```
재실행
```/bin/bash
systemctl restart nexus.service
```

기존 admin, 설정한 password로 로그인된다면 성공!!
![](https://i.imgur.com/v3TRUsH.png)

db도 살아있나 확인하고
![](https://i.imgur.com/3VdIfE0.png)

백업내용 모두 지우고 재실행
```/bin/bash
rm /opt/sonatype-work/nexus3/restore-from-backup/*
systemctl restart nexus.service
```



참조 블로그
https://ko.linux-console.net/?p=2997#gsc.tab=0




