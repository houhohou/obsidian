#ovirt #debian 

[[Ovirt]] 설치 
## Self hosted engine

구조는 아래와 같은데, Host 1 만 올릴거다. 
![](https://i.imgur.com/0oqRBts.png)

rocky 8.8 에 overt 4.4.10을 설치
메인 IP : 10.0.0.1
가상머신 1개 IP : 10.0.0.2

100G ( 기본 )
100G ( nfs용 )

#중요 nfs storage 사용 시, 최소 61GB 이상 필요하므로 마운트 대상 디렉터리 용량 최소 100GB 이상 필요


##### 기본 미들웨어 작업
```bash
#호스트 이름 설정
hostnamectl set-hostname manager

#selinux 끄기
setenforce 0
```

##### NFS할거면 아래 입력
```bash
#nfs 설치
dnf install nfs-utils -y

#공유 디렉토리 설정
mkdir /storage
chmod 0777 /storage
chown 36:36 /storage/
cat >> /etc/exports << E0F
/storage *(rw,no_root_squash)
E0F

#마운트 할 시 작업할것
mkfs.ext4 /dev/sdb
mount /dev/sdb /storage

#NFS 재시작 & 확인
systemctl restart rpcbind
systemctl restart nfs-server
exportfs

# NFS방화벽 오픈
﻿firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --reload
```

sed -i 's|#PermitRootLogin yes|PermitRootLogin yes|g' *
sed -i 's|#PubkeyAuthentication yes|PubkeyAuthentication yes|g' /etc/ssh/sshd_config

## DNS 설정 & SSH

```bash
#IP & 네임서버 설정
cat >> /etc/hosts << E0F
10.0.0.1 manager
10.0.0.2 master.ovirt.com
E0F

#ssh 설정 & 재시작
vi /etc/ssh/sshd_config
PermitRootLogin yes
sed -i 's|#PubkeyAuthentication yes|PubkeyAuthentication yes|g' /etc/ssh/sshd_config

systemctl restart sshd
```



## 배포전 준비 & 실행
```bash
#ovirt rpm 설치 ( 레포지토리도 최신화 된다. )
dnf -y install https://resources.ovirt.org/pub/yum-repo/ovirt-release44.rpm

#dnf 호환버전설치되게 변경
dnf distro-sync --nobest
dnf upgrade --nobest

#ovirt 설치
dnf install -y ovirt-engine-appliance
dnf install -y ovirt-hosted-engine-setup
sudo dnf -y install cockpit cockpit-ovirt-dashboard  

#권한주기
sudo usermod -a -G kvm vdsm

#cockpit 방화벽 설정
systemctl enable  --now cockpit.socket
sudo firewall-cmd --add-service=cockpit
sudo firewall-cmd --add-service=cockpit --permanent
sudo firewall-cmd --reload
firewall-cmd --permanent --add-service={http,https}
firewall-cmd --permanent --add-port=80/tcp 
firewall-cmd --permanent --add-port=443/tcp 
firewall-cmd --permanent --add-port=8080-8090/tcp  
firewall-cmd --reload

#권한 주기
chmod 777 /var/lib/ovirt-hosted-engine-ha

#설치할때 아래버전으로 설치되게 고정
dnf module -y enable javapackages-tools
dnf module -y enable pki-deps
dnf module -y enable postgresql:12
dnf module -y enable nodejs:14
dnf module -y enable mod_auth_openidc:2.3

#아래경로 들어가서 true로 변경
vi /usr/share/ansible/collections/ansible_collections/ovirt/ovirt/roles/hosted_engine_setup/defaults/main.yml
he_pause_before_engine_setup: flase => true

#vdsm 업그레이드 4.100
dnf upgrade vdsm
```
sed -i 's|he_pause_before_engine_setup: false|he_pause_before_engine_setup: true|g' /usr/share/ansible/collections/ansible_collections/ovirt/ovirt/roles/hosted_engine_setup/defaults/main.yml

#### 설치시작
```bash
hosted-engine --deploy
```


중간에 한번 멈추는데, /tmp/ansible.~~~ 지우라고 뜬다. 그부분에서 바로 지우지말고 아래 부분 작업
#### 중간설정부분
##### node에서 해야할 부분
```bash
#libvirtzone에 방화벽 설정
firewall-cmd --permanent --zone=libvirt --add-port=80/tcp
firewall-cmd --permanent --zone=libvirt --add-service={http,https}
firewall-cmd --permanent --zone=libvirt --add-port=443/tcp 
firewall-cmd --permanent --zone=libvirt --add-port=8080-8090/tcp  
firewall-cmd --reload
firewall-cmd --permanent --zone=libvirt --add-service=nfs
firewall-cmd --permanent --zone=libvirt --add-service=mountd
firewall-cmd --permanent --zone=libvirt --add-service=rpc-bind
firewall-cmd --reload
```
##### master에서 해야할 부분 ( 외부통신에 따라 두가지 방법으로 진행 )
외부통신이 되는환경이면 **Public_only**  만 진행, 내부통신만 하면 **Priviate_only** 진행

**Public_only**
``` bash
#selinux 끄기
setenforce 0

#httpd 시작
systemctl restart httpd

#postgresql 업데이트 되지않게 설정
echo "exclude=postgresql-jdbc" >> /etc/dnf/dnf.conf

#방화벽에서 http 오픈
firewall-cmd --permanent --add-service={http,https}
firewall-cmd --permanent --add-port=80/tcp 
firewall-cmd --permanent --add-port=443/tcp 
firewall-cmd --permanent --add-port=8080-8090/tcp  
firewall-cmd --reload
```

**Priviate_only**
```bash
#selinux 끄기
setenforce 0

#httpd 시작
systemctl restart httpd

#postgresql 업데이트 되지않게 설정
echo "exclude=postgresql-jdbc" >> /etc/dnf/dnf.conf

#방화벽에서 http 오픈
firewall-cmd --permanent --add-service={http,https}
firewall-cmd --permanent --add-port=80/tcp 
firewall-cmd --permanent --add-port=443/tcp 
firewall-cmd --permanent --add-port=8080-8090/tcp  
firewall-cmd --reload

#레포지토리 백업
mkdir ~/repo.bak
mv /etc/yum.repos.d/* ~/repo.bak/
mkdir ~/gpg.bak
mv /etc/pki/rpm-gpg/* ~/gpg.bak/
#node01레포지토리 & gpg키 받아오기 받아오기
scp root@10.0.0.1:/etc/yum.repos.d/* /etc/yum.repos.d/
scp root@10.0.0.1:/etc/pki/rpm-gpg/* /etc/pki/rpm-gpg/
sed -i 's|192.168.10.223|192.168.10.10|g' *
#설치과정에서 repo파일변경되지않게 고정
chmod 444 /etc/yum.repos.d/*
sudo chattr +i /etc/yum.repos.d/*
```
sudo chattr -i /etc/yum.repos.d/
scp root@10.244.10.14:/repo/yum.file/yum.http/* /etc/yum.repos.d/
scp root@10.244.10.14:/etc/pki/rpm-gpg/* /etc/pki/rpm-gpg/

## 작업과정 오류 
192.168.10.10
sed -i 's|192.168.10.10|10.244.10.14|g' *
10.244.10.14/repo/
sed -i 's|10.244.10.14/repo/|10.244.10.14/repo/rocky8.8new/|g' *
오류때문에 날리는 명령어
/usr/sbin/ovirt-hosted-engine-cleanup
egrep '(vmx|svm)' /proc/cpuinfo
수동다운그레이드
dnf downgrade postgresql-jdbc
## 참고링크
ovirt 공식 다운로드
https://www.ovirt.org/download/

이거따라하니깐 됨
https://computingforgeeks.com/install-and-configure-ovirt-on-centos/?expand_article=1

참조노션
https://shining-mackerel-05f.notion.site/ovirt-5e14e74f0ca94ff1baad53ce5b5c25f8


fatal: [localhost]: FAILED! => {"changed": false, "msg": "The host has been set in non_operational status, deployment errors:   code 505: Host node01.ovirt installation failed. Failed to configure management network on the host.,    code 9000: Failed to verify Power Management configuration for Host node01.ovirt.,    code 10802: VDSM node01.ovirt command Get Host Capabilities failed: Internal JSON-RPC error: {'reason': \"'dhcp'\"},   fix accordingly and re-deploy."}


```bash
    <serial type='pty'>
      <target type='isa-serial' port='0'>
        <model name='isa-serial'/>
      </target>
    </serial>
    <console type='pty'>
      <target type='serial' port='0'/>
    </console>
```





# 이슈 1
https://forums.unraid.net/topic/131240-libvirt-error-virnetsocketreadwire1791-end-of-file-while-reading-data-inputoutput-error-after-upgrading-to-6115

![](https://i.imgur.com/unFKJ03.png)





