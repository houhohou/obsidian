#리눅스 





- [b] 준비사항

| 이름 | Disk | 크기 | OS | IP | 설명 | Pub IP |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| proxy | - | - | ubuntu20.04 | 10.185.214.65 | ansible서버 | 192.168.34.55 |
| ceph01 | /dev/sdc | 100G | ubuntu20.04 | 10.185.214.66 | 스토리지1 | 192.168.34.67 |
| ceph02 | /dev/sdc | 100G | ubuntu20.04 | 10.185.214.67 | 스토리지2 | 192.168.34.56 |
| ceph03 | /dev/sdd | 100G | ubuntu20.04 | 10.185.214.68 | 스토리지3 | 192.168.34.36 |


- [b] Package설치 
```bash
apt update
apt install python3 python3-pip sshpass
cat >> /etc/hosts << E0F
127.0.0.1 localhost
10.101.0.14 node0
10.101.0.4 node1
10.101.0.15 node2
E0F
```

- [b] 앤서블 다운로드 
```bash
#deploy
git clone https://github.com/ceph/ceph-ansible.git
```

버전을 맞추는데 버전은 
- [b] stable버전 설정하면됨 (여기서는 stable-7.0했음)
![250](https://i.imgur.com/N2OVyVz.png)
- [b] 버전맞춰준다
```bash
#deploy
cd ceph-ansible
git checkout stable-7.0
```
- [b] 가상python 
```bash
sudo apt install python3.8-pip
python3 -m venv ceph-venv
# 가상환경 실행
. ceph-venv/bin/activate
```
- [b] 사전패키지들 다운받아준다. ( 버전안맞으면 안되니깐 버전맞춰주기 ) (이거에서 파이썬 버전맞추고, 앤서블 버전맞추는데 개 죽쒓는데 지피티 이용하자.... 다운그레이드 업그레이드 개 난리침)
```bash
#deploy
pip install -r requirements.txt

django-appconf==1.0.3 django-babel==0.6.2 django-compressor==2.4 django-debreach==2.0.1 django-pyscss==2.0.2
```
- [b] 레포지토리추가 
```bash
sudo add-apt-repository ppa:ansible/ansible
```