[[debian]] 전용 [[repo]] 작업


사설레포지를 설정하는게 아니라

사설레포지를 만드는법
httpd를 생성, 레포 하나씩 지정, 
```bash
yum -y install httpd
yum -y install createrepo
yum -y install yum-utils 
```


httpd설정
```bash
systemctl enable --now httpd
```

레포지토리 지정하는 방법
```bash
mkdir /var/www/html/
yum repolist

reposync -m --repoid=<repoid> --download-metadata --download-path=/var/www/html/<repoid>

```

rpm가져와서 압축푸는법
```bash
rpm -ivh --nodeps --force *
rpm -ivh --nodeps --force wxGTK3-3.0.5.1-1.el8.x86_64.rpm
yum install -y rabbitmq-server
```

percona 설치
```bash
dnf module disable mysql 
yum install -y percona-xtradb-cluster
```

pacemaker 설치
```bash
yum install -y pacemaker-2.0.5-9.el8
yum install pcs

```

권한문제일때
```bash
sudo chcon -R -t httpd_sys_content_t /var/www/html/repo
```

