


- [b] 구상도
![](https://i.imgur.com/WazIgeP.png)


| 설명 | name | Disk | size | OS | IP | Public IP |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 컨트롤 서버 | proxy | - | - | ubuntu20.04 | 10.185.214.65 | 192.168.34.55 |
| 노드 1 | ceph01 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.66 | 192.168.34.67 |
| 노드 2 | ceph02 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.67 | 192.168.34.56 |
| 노드 3 | ceph03 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.68 | 192.168.34.36 |

- [i]  전제조건
1. **운영 체제**: 모든 노드에 호환되는 Linux 배포판(예: CentOS 또는 Ubuntu)
2. **Python3**: `cephadm`에는 Python 3
3. **SSH 액세스**: 배포 노드에서 다른 모든 노드로의 비밀번호 없는 SSH 액세스
4. **sudo 권한을 가진 사용자**: 모든 노드에 대해 sudo 권한을 가진 사용자
5. **방화벽 및 SELinux**: 모든 노드에서 방화벽 및 SELinux를 적절하게 구성하거나 비활성화.
6. **시간 동기화**: NTP가 구성되었고 시간이 모든 노드에서 동기화.
7. **Docker 또는 Podman**: 컨테이너 관리를 위해 Docker 또는 Podman이 설치되어 있는지 확인.

- [b] 기본세팅 
```bash
# 1,2,3,proxy에 docker 설치
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker


# proxy
curl --silent --remote-name --location https://github.com/ceph/ceph/raw/octopus/src/cephadm/cephadm 
chmod +x cephadm 
sudo mv cephadm /usr/local/bin

# 모니터링 ip추가 ( 여기서는 proxy서버로 했음 )
sudo cephadm bootstrap --mon-ip 10.185.214.65
~~~
Ceph Dashboard is now available at:

	     URL: https://proxy:8443/
	    User: admin
	Password: 4g81v5vvb4
~~~~

# 명령어 설치
cephadm install ceph-common

# 클러스터 상태 확인
ceph -s

# ssh 키 복사
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph01
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph02
ssh-copy-id -f -i /etc/ceph/ceph.pub root@ceph03

# 호스트 붙이기
ceph orch host add ceph01
ceph orch host label add ceph01 _admin
ceph orch host add ceph02
ceph orch host label add ceph02 _admin
ceph orch host add ceph03
ceph orch host label add ceph03 _admin

# 호스트 붙었는지 확인
ceph orch host ls
HOST    ADDR    LABELS  STATUS
ceph01  ceph01  _admin
ceph02  ceph02  _admin
ceph03  ceph03  _admin
proxy   proxy

# 호스트들에 디바이스 붙이기
ceph orch daemon add osd ceph01:/dev/sdb
ceph orch daemon add osd ceph02:/dev/sdb
ceph orch daemon add osd ceph03:/dev/sdb
```
- [b] 명령어 사용방법
```bash
# mon daemon이 잘 배포되었는지 확인
ceph -s
  cluster:
    id:     7e18b366-b676-11ee-839e-eb79d9d0f1ae
    health: HEALTH_WARN
            mon proxy is low on available space

  services:
    mon: 3 daemons, quorum proxy,ceph03,ceph02 (age 33m)
    mgr: proxy.nlpsfa(active, since 2h), standbys: ceph01.etgbtz
    osd: 3 osds: 3 up (since 31m), 3 in (since 31m)

  data:
    pools:   1 pools, 1 pgs
    objects: 0 objects, 0 B
    usage:   3.0 GiB used, 297 GiB / 300 GiB avail
    pgs:     1 active+clean
# 명령어 2
ceph osd tree
ID  CLASS  WEIGHT   TYPE NAME        STATUS  REWEIGHT  PRI-AFF
-1         0.29306  root default
-3         0.09769      host ceph01
 0    ssd  0.09769          osd.0        up   1.00000  1.00000
-5         0.09769      host ceph02
 1    ssd  0.09769          osd.1        up   1.00000  1.00000
-7         0.09769      host ceph03
 2    ssd  0.09769          osd.2        up   1.00000  1.00000

# osd로 추가할 수 있는 device 확인
ceph orch device ls
Hostname  Path      Type  Serial  Size   Health   Ident  Fault  Available
ceph01    /dev/sdb  ssd            107G  Unknown  N/A    N/A    No
ceph02    /dev/sdb  ssd            107G  Unknown  N/A    N/A    No
ceph03    /dev/sdb  ssd            107G  Unknown  N/A    N/A    No
```

- [i] docker ps 를 해보면 다 설치되어있다. 
![400](https://i.imgur.com/euyj1bT.png)


- [b] 접속
![](https://i.imgur.com/0k94E5P.png)



- [!] 참고 블로그
https://vittorio-lee.tistory.com/91


https://wcc8088.tistory.com/127

