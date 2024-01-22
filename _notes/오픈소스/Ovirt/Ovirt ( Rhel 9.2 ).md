#ovirt #debian 

[[Ovirt]] 설치 

## Self hosted engine

사이트에 Rhel에만 해당하는 작업
https://ovirt.org/download/install_on_rhel.html



에러로그

![](https://i.imgur.com/ACFk3WA.png)


noarch 여기 있긴한데 gpg키를 못찾겠음
https://linuxsoft.cern.ch/cern/centos/s9/BaseOS/xhttps://linuxsoft.cern.ch/cern/centos/s9/BaseOS/x86_64/os/Packages/86_64/os/Packages/

centos-stream-release-9.0-12.el9.noarch.rpm



- [1] 작업내용
```bash
dnf install ovirt-hosted-engine-setup


vi /usr/share/ansible/collections/ansible_collections/ovirt/ovirt/roles/hosted_engine_setup/defaults/main.yml
he_pause_before_engine_setup: flase => true

dnf -y install tmux

hosted-engine --deploy --4
```



admin@ovirt
cloud1234
