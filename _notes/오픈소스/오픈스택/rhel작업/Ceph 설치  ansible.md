#openstack 
[[오픈스택]] 앤서블 돌리기 전에 [[ceph]] 작업

rhel 8.4
ceph quincy version
공홈 레포지토리 사용할것이다.
vsphere로 500기가 추가 로 붙였다. 

서버
deploy (앤서블 돌릴서버)
army01, army02, army03 (ceph cluster)


사전작업
hostname 추가 
```/bin/bash
#deploy,1,2,3
cat >> /etc/hosts << E0F
192.168.34.8 army01
192.168.34.9 army02
192.168.34.7 army03
192.168.34.11 army04
192.168.34.12 army05
E0F
```

앤서블 다운로드 
```/bin/bash
#deploy
git clone https://github.com/ceph/ceph-ansible.git
```

버전을 맞추는데 버전은 

stable버전 설정하면됨 (여기서는 stable-7.0했음)

![](https://i.imgur.com/N2OVyVz.png)

버전맞춰준다
```/bin/bash
#deploy
cd ceph-ansible
git checkout stable-7.0
```


사전패키지들 다운받아준다. ( 버전안맞으면 안되니깐 버전맞춰주기 ) (이거에서 파이썬 버전맞추고, 앤서블 버전맞추는데 개 죽쒓는데 지피티 이용하자.... 다운그레이드 업그레이드 개 난리침)
```/bin/bash
#deploy
pip install -r requirements.txt

django-appconf==1.0.3 django-babel==0.6.2 django-compressor==2.4 django-debreach==2.0.1 django-pyscss==2.0.2

```




레포지토리추가 
```/bin/bash
#deploy,1,2,3
cat >> /etc/yum.repos.d/rhel8.4.repo << E0F
[ceph_stable]
baseurl = http://download.ceph.com/rpm-quincy/el8/x86_64
gpgcheck = 1
gpgkey = https://download.ceph.com/keys/release.asc
name = Ceph Stable x86_64h repo
priority = 2

[ceph_stable_noarch]
baseurl = http://download.ceph.com/rpm-quincy/el8/noarch
gpgcheck = 1
gpgkey = https://download.ceph.com/keys/release.asc
name = Ceph Stable noarch repo
priority = 2

[ceph_stable_aarch64]
baseurl = http://download.ceph.com/rpm-quincy/el8/aarch64
gpgcheck = 1
gpgkey = https://download.ceph.com/keys/release.asc
name = Ceph Stable aarch64 repo
priority = 2


[ceph_stable_SRPMS]
baseurl = http://download.ceph.com/rpm-quincy/el8/SRPMS
gpgcheck = 1
gpgkey = https://download.ceph.com/keys/release.asc
name = Ceph Stable SRPMS repo
priority = 2

E0F
```


ansible hosts 설정
```/bin/bash
#deploy
cat >> /root/ceph-ansible/inventory.ini << E0F
[mons]
army01
army02
army03

[osds]
army01
army02
army03


[mgrs]
army01
army02
army03

[monitoring]
army01
army02
army03
E0F


group_vars/all.yml 을 변경해주는데 아래정도 넣어줌

```/bin/bash
vi group_vars/all.yml

dummy:
cluster: ceph
ntp_service_enabled: true
ntp_daemon_type: chronyd
ceph_origin: distro
#ceph_origin: repository
ceph_repository: rhcs
ceph_mirror: https://download.ceph.com
ceph_stable_key: https://download.ceph.com/keys/release.asc
ceph_stable_release: quincy
ceph_stable_repo: "{{ ceph_mirror }}/rpm-{{ ceph_stable_release }}"
monitor_interface: ens33
public_network: 192.168.34.0/24
cluster_network: 192.168.34.0/24
dashboard_admin_user: admin
dashboard_admin_password: cloud1234
grafana_admin_user: admin
grafana_admin_password: cloud1234
```
#ceph_repository 이건 OS 버전맞추는거 ( rhcs는 rhel말하는거)
#ceph_mirror 레포지토리 위치 설정
#ceph_stable_release 레포지토리 서버 안에 드가보면 서버여러개있는데 그거 고정시켜줌


group_vars/osds.yml 설정 (db경로 지정해준다)
```/bin/bash
vi group_vars/osds.yml
dummy:
copy_admin_key: true
devices:
  - /dev/sdb
```


```/bin/bash
ansible-playbook -i inventory.ini site.yml
```


```/bin/bash

```


```/bin/bash

```


```/bin/bash

```


```/bin/bash

```


```/bin/bash

```






















공식홈페이지 앤서블로 설치 우에하는지 나온다. 

https://docs.ceph.com/projects/ceph-ansible/en/latest