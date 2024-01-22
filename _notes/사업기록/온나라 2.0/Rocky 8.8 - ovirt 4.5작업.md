---
작업일: 2023-12-16
작업 기록일지: 버전테스트
진행속도:
---
### 기본 베이스
```bash
hostnamectl set-hostname node01.ovirt.com

#selinux 끄기
setenforce 0

cat >> /etc/hosts << E0F
10.0.0.1 node01.ovirt.com
10.0.0.2 master.ovirt.com
E0F

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

```

설치작업
```bash
dnf module reset virt
dnf module enable virt:rhel -y

dnf -y install centos-release-ovirt45
dnf -y install ovirt-engine-appliance

dnf -y install ovirt-hosted-engine-setup
```

### 버전확인

centos-release-ovirt45 선택권 없음
![](https://i.imgur.com/QLcWd3I.png)

ovirt-engine-appliance 선택권 없음
![](https://i.imgur.com/pYmMU6C.png)


방화벽등
```bash
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

#아래 변경
vi /usr/share/ansible/collections/ansible_collections/ovirt/ovirt/roles/hosted_engine_setup/defaults/main.yml
he_pause_before_engine_setup: flase => true

```

시작
```bash
hosted-engine --deploy --4
```


```bash
#중간단계
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
## 작업내용

31 - > enablre

232 -> disable

241, 248