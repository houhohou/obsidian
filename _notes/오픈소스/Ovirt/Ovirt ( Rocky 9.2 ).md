#ovirt #debian 

[[Ovirt]] 설치

## Self hosted engine


❗홈페이지에 있는 기본적으로 할 것들
```bash
cat >/etc/yum.repos.d/CentOS-Stream-Extras-common.repo <<'EOF'
[c9s-extras-common]
name=CentOS Stream $releasever - Extras packages
metalink=https://mirrors.centos.org/metalink?repo=centos-extras-sig-extras-common-$stream&arch=$basearch&protocol=https,http
gpgkey=https://www.centos.org/keys/RPM-GPG-KEY-CentOS-SIG-Extras
gpgcheck=1
repo_gpgcheck=0
metadata_expire=6h
countme=1
enabled=1
EOF

echo "9-stream" > /etc/yum/vars/stream


dnf distro-sync --nobest
- N
dnf install -y centos-release-ovirt45
```
##### nfs 작업
```bash
#유저 & 폴더 생성

useradd vdsm -u 36 -g 36 -s /sbin/nologin -M -d /

chown -R vdsm:kvm /storage
chmod 777 /storage
sudo usermod -a -G kvm vdsm
```

❗그냥 ovirt-engine-appliance받으니깐 문제생김
```bash
wget https://resources.ovirt.org/pub/ovirt-4.5/rpm/el8/x86_64/ovirt-engine-appliance-4.5-20231201120252.1.el8.x86_64.rpm

dnf install -y ./ovirt-engine-appliance-4.5-20231201120252.1.el8.x86_64.rpm

dnf install ovirt-engine-appliance

vi /usr/share/ansible/collections/ansible_collections/ovirt/ovirt/roles/hosted_engine_setup/defaults/main.yml
he_pause_before_engine_setup: flase => true

#호스트이름 설정 
hostnamectl set-hostname node01.ovirt.com

cat >>  /etc/hosts << E0F
10.185.214.151 node01.ovirt.com  #실제pc IP
10.185.214.152 master.ovirt.com. #가상머신 IP
E0F

#방화벽
systemctl restart ovirt-engine
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
firewall-cmd --permanent --add-service={http,https}
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=443/tcp
firewall-cmd --permanent --add-port=8080-8090/tcp
firewall-cmd --reload

firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --
```
#### 설치시작
```bash
hosted-engine --deploy --4
```
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
**Public_only**
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
