#ovirt #debian 

[[Ovirt]] 설치

## Self hosted engine
##### nfs 작업
```bash
#유저 & 폴더 생성

useradd vdsm -u 36 -g 36 -s /sbin/nologin -M -d /

chown -R vdsm:kvm /storage
chmod 777 /storage
sudo usermod -a -G kvm vdsm
```


```bash

#호스트이름 설정 

cat >>  /etc/hosts << E0F

10.185.214.151 node01.ovirt.com  #실제pc IP
10.185.214.152 master.ovirt.com. #가상머신 IP
E0F

hostnamectl set-hostname noce01.ovirt.com


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
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --reload


#Ovirt 설치
dnf install -y centos-release-ovirt45

dnf -y install ovirt-hosted-engine-setup

#파이썬 파일 수정

sed -i 's/from collections import Callable/from collections.abc import Callable/g' /usr/share/ovirt-hosted-engine-setup/he_ansible/callback_plugins/2_ovirt_logger.py

#설치
dnf install -y tmux

tmux

hosted-engine --deploy --4
```


https://www.reddit.com/r/ovirt/comments/zbq7ws/installing_self_hosted_ovirt_453_on_rocky_linux_91/



```
dnf distro-sync --nobest
```

```
dnf install ovirt-engine-appliance
```

```
# systemctl unmask firewalld
# systemctl start firewalld
```


```bash
#가상머신 들어가서 아래
echo "exclude=postgresql-jdbc" >> /etc/dnf/dnf.conf
```




아래거보고 하니 도미!
https://blog.csdn.net/m0_37817456/article/details/133126384




에러로그 
vi ~/ovirt-log-2.log
	6013번 줄
```bash
 6015 Traceback (most recent call last):
   6016   File "/usr/lib/python3.9/site-packages/otopi/context.py", line 132, in _executeMethod
   6017     method['method']()
   6018   File "/usr/share/ovirt-hosted-engine-setup/scripts/../plugins/gr-he-ansiblesetup/core/misc.py", line 509, in _closeup
   6019     raise RuntimeError(_('Failed executing ansible-playbook'))
   6020 RuntimeError: Failed executing ansible-playbook
```
https://lists.ovirt.org/archives/list/users@ovirt.org/message/2PAQ6ELTDMH4IQREAP3AWX7COU2L5KGJ/


dnf install -y python3-pip

pip3 install --upgrade pip

pip3 install ansible-core==2.13.0


```
hosted-engine --deploy --restore-from-file=ovirt4.5.backup --4
```





```
yum install python-passlib
```

https://github.com/oVirt/ovirt-ansible-collection





[[Ovirt]]