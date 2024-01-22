[[ubuntu]]에서 시간동기화 하는것, 

중요한이유 : DB같은데 데이터 집어넣을때, 모두 시간이 같지 않으면 DB가 어느 데이터가 최신인지 인식못함

10.0.0.1 deploy ( 시간동기화 될 서버 )
10.0.0.2 server01
10.0.0.3 server02
10.0.0.4 server03

##### 작업시작
모든 서버
```bash
#설치 후 백업
apt -y install chrony
cp -p /etc/chrony/chrony.conf{,.bak}
```
deploy서버 제외 모두
```bash
#네임서버 설정
cat >> /etc/hosts << E0F
10.10.10.1 deploy
E0F
```
deploy서버
```bash
#시간 동기화
sed -i -e "/pool / s/^/#/" /etc/chrony/chrony.conf

cat >> /etc/chrony/chrony.conf << E0F
server kr.pool.ntp.org iburst
server time.bora.net iburst
server time.nuri.net iburst
allow 10.10.10.0/24
E0F
```
server01,server02,server03 진행
```bash
#바라보게끔 설정
sed -i -e "/pool / s/^/#/" /etc/chrony/chrony.conf

cat << EOF >> /etc/chrony/chrony.conf
server 10.10.10.1 iburst
EOF

```
deploy서버
```bash
## chrony 접근 포트번호 허용
ufw allow 123/udp && ufw allow 323/udp

```
모든 서버 재시작( deploy서버먼저 )
```bash
systemctl enable chrony
systemctl restart chrony
```
상태 확인
```bash
chronyc sources
```

하드웨어 시간을 시스템시간으로 동기화
```bash
hwclock --systohc
```

