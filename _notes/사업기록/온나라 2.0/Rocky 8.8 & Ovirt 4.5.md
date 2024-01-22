## 링크
##### 공홈 RHEL
https://www.ovirt.org/download/install_on_rhel.html
##### 공홈 install
https://www.ovirt.org/documentation/installing_ovirt_as_a_self-hosted_engine_using_the_command_line/index.html


##### centos 8 설치하는과정
https://www.server-world.info/en/note?os=CentOS_Stream_8&p=ovirt45&f=2

```bash


#rocky
dnf install -y dnf-plugins-core
dnf config-manager --set-disabled extras

# On Rocky there's an issue with centos-release-nfv package from extras
dnf install -y dnf-plugins-core
dnf config-manager --set-disabled extras
cat >/etc/yum.repos.d/CentOS-Stream-Extras.repo <<'EOF'
[cs8-extras]
name=CentOS Stream $releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=8-stream&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://mirror.centos.org/$contentdir/8-stream/extras/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=https://www.centos.org/keys/RPM-GPG-KEY-CentOS-Official
EOF

cat >/etc/yum.repos.d/CentOS-Stream-Extras-common.repo <<'EOF'
[cs8-extras-common]
name=CentOS Stream $releasever - Extras common packages
mirrorlist=http://mirrorlist.centos.org/?release=8-stream&arch=$basearch&repo=extras-extras-common
#baseurl=http://mirror.centos.org/$contentdir/8-stream/extras/$basearch/extras-common/
gpgcheck=1
enabled=1
gpgkey=https://www.centos.org/keys/RPM-GPG-KEY-CentOS-SIG-Extras
EOF


echo "8-stream" > /etc/yum/vars/stream

dnf update
```



```bash
# 백업
mkdir ~/repo.bak
mv /etc/yum.repos.d/* ~/repo.bak/
mkdir ~/gpg.bak
mv /etc/pki/rpm-gpg/* ~/gpg.bak/

# 레포지토리 락
sudo chattr +i /etc/yum.repos.d/*
sudo chattr +i /etc/pki/rpm-gpg/*
```


```bash
dnf module reset virt
dnf module enable virt:rhel
dnf -y install ovirt-engine-appliance
dnf -y install centos-release-ovirt45
dnf -y install ovirt-hosted-engine-setup

dnf module -y enable javapackages-tools
dnf module -y enable pki-deps
dnf module -y enable postgresql:12
dnf module -y enable nodejs:14
dnf module -y enable mod_auth_openidc:2.3

dnf module -y disable javapackages-tools
dnf module -y disable pki-deps
dnf module -y disable postgresql:12
dnf module -y disable nodejs:14
dnf module -y disable mod_auth_openidc:2.3

#권한주기
sudo usermod -a -G kvm vdsm
chmod 777 /var/lib/ovirt-hosted-engine-ha

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
```



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

버전
![](https://i.imgur.com/DO4Qrzk.png)
![](https://i.imgur.com/iSYZcS2.png)
![](https://i.imgur.com/itK19cp.png)

![](https://i.imgur.com/fOnIrXv.png)


가상머신 내부에서 한 작업
``` bash
#selinux 끄기
setenforce 0

#httpd 시작
systemctl restart httpd


#방화벽에서 http 오픈
firewall-cmd --permanent --add-service={http,https}
firewall-cmd --permanent --add-port=80/tcp 
firewall-cmd --permanent --add-port=443/tcp 
firewall-cmd --permanent --add-port=8080-8090/tcp  
firewall-cmd --reload
```

내부머신 
![](https://i.imgur.com/F2h7o1o.png)

## 작업과정 오류 

오류때문에 날리는 명령어
/usr/sbin/ovirt-hosted-engine-cleanup

가상화 지원확인
egrep '(vmx|svm)' /proc/cpuinfo


수동다운그레이드
dnf downgrade postgresql-jdbc



2023-12-15 10:29:28,566+0900 INFO  (jsonrpc/1) [api.host] START getAllVmStats() from=::1,59710 (api:31)
2023-12-15 10:29:28,568+0900 INFO  (jsonrpc/1) [api.host] FINISH getAllVmStats return={'status': {'code': 0, 'message': 'Done'}, 'statsList': (suppressed)} from=::1,59710 (api:37)
2023-12-15 10:29:29,889+0900 INFO  (vmrecovery) [vdsm.api] START getConnectedStoragePoolsList() from=internal, task_id=467b4c19-3838-4deb-bc2d-a72d0fa1fc44 (api:31)
2023-12-15 10:29:29,889+0900 INFO  (vmrecovery) [vdsm.api] FINISH getConnectedStoragePoolsList return={'poollist': []} from=internal, task_id=467b4c19-3838-4deb-bc2d-a72d0fa1fc44 (api:37)
2023-12-15 10:29:29,889+0900 INFO  (vmrecovery) [vds] recovery: waiting for storage pool to go up (clientIF:723)

dnf module -y disable javapackages-tools
dnf module -y disable pki-deps
dnf module -y disable postgresql:12
dnf module -y disable nodejs:14
dnf module -y disable mod_auth_openidc:2.3

vdsm 안될때
```bash
vdsm-tool configure --force
 systemctl restart vdsmd
```



Ovirt 4.5 설치 진행상황
가이드라인대로 4.5 설치 시 module 문제 발생으로 모든 모듈 disable (해결)
설치과정에서 vdsm이 제대로 작동하지 않아, 엔진이 뜨지않는 이슈 발생. ( 현재 문제 확인중 )
vdsm이 restart 되는 과정에서 libvirtd lock 상황 발생으로 vdsm 이슈와 겹치는지 확인중
위 문제로 인해 vdsm version downgrade 해도 같은 이슈 발생.
