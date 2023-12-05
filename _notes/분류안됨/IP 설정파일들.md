


```/bin/bash
192.168.34.47 오픈스택 올인원

192.168.34.45 deploy
192.168.34.46 proxy01
192.168.34.49 proxy02
```
```/bin/bash
10.185.214.210 deploy
10.185.214.211 proxy01
10.185.214.212 proxy02
10.185.214.221 master01
10.185.214.222 master02
10.185.214.223 master03
10.185.214.226 node01
10.185.214.227 node02
10.185.214.228 node03
```

# 아직안한것들
```/bin/bash

hwclock --hctosys

cp -p /etc/security/limits.conf{,.bak}

cat << EOF >> /etc/security/limits.conf

*               soft    nofile          65536
*               hard    nofile          65536
root            soft    nofile          65536
root            hard    nofile          65536
EOF
reboot

```

apt repo바라보게 하는 명령어들
```/bin/bash

#‘deb’를 ‘deb [arch=amd64]’로 변경
sudo sed -i 's/\bdeb\b/deb [arch=amd64]/g' /etc/apt/sources.list
# 기존 repo를 deploy 서버의 내부망 ip로 변경
sudo sed -i 's/archive\.ubuntu\.com/10.185.214.210:10880/g' /etc/apt/sources.list



apt -y install chrony
exit


```
chrony설정
```/bin/bash

#모든서버
apt -y install chrony --fix-missing
cp -p /etc/chrony/chrony.conf{,.bak}

# deploy제외 모든 서버
cat << EOF >> /etc/hosts
10.185.214.210 deploy
EOF

#deploy서버만 설정
sed -i -e "/pool / s/^/#/" /etc/chrony/chrony.conf
cat << EOF >> /etc/chrony/chrony.conf

server kr.pool.ntp.org iburst
server time.bora.net iburst
server time.nuri.net iburst

allow 10.185.214.0/24
EOF
ufw allow 123/udp && ufw allow 323/udp


#deploy서버 제외 모든 서버 
sed -i -e "/pool / s/^/#/" /etc/chrony/chrony.conf
cat << EOF >> /etc/chrony/chrony.conf

server 10.185.214.210 iburst
EOF

#모든 서버 진행 (deploy서버 먼저)
systemctl enable chrony
systemctl restart chrony
chronyc sources





```
#'chrony sources'했을때  아래처럼 뜨면 안되고
![](https://i.imgur.com/XmS6WPe.png)
#'chrony sources'했을때  아래처럼 떠야함! 
![](https://i.imgur.com/Zof1Wlq.png)



```/bin/bash
#모든서버에서 진행  하드웨어시간 동기화

hwclock --systohc
```