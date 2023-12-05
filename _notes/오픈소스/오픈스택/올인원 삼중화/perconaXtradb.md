#openstack 
[[ubuntu 레포지터리]]


all
```/bin/bash
sudo apt update
sudo apt install -y wget gnupg2 lsb-release curl --fix-missing
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
sudo dpkg -i percona-release_latest.generic_all.deb
sudo apt update
sudo percona-release setup pxc80
sudo apt install -y percona-xtradb-cluster --fix-missing

systemctl stop mysql

ufw allow 3306
ufw allow 4444
ufw allow 4567
ufw allow 4568
```

https://docs.percona.com/percona-xtradb-cluster/8.0/apt.html#prerequisites


https://docs.percona.com/percona-xtradb-cluster/8.0/configure-nodes.html


# 컨트롤러 1

#/etc/mycnf
```/bin/bash
[client]
socket=/var/run/mysqld/mysqld.sock

[mysqld]
server-id=1
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
log-error=/var/log/mysql/error.log
pid-file=/var/run/mysqld/mysqld.pid

binlog_expire_logs_seconds=604800

wsrep_provider=/usr/lib/galera4/libgalera_smm.so

wsrep_cluster_address=gcomm://10.185.14.251,10.185.14.252,10.185.14.253

binlog_format=ROW

wsrep_slave_threads=8

wsrep_log_conflicts

innodb_autoinc_lock_mode=2

wsrep_cluster_name=pxc-cluster

wsrep_node_name=koo-controller1
wsrep_node_address=10.185.14.251

pxc_strict_mode=ENFORCING

wsrep_sst_method=xtrabackup-v2

pxc-encrypt-cluster-traffic=OFF

max_connections=20000
bind-address=0.0.0.0
```


# 컨트롤러 2
```/bin/bash
# controller node2
[client]
socket=/var/run/mysqld/mysqld.sock

[mysqld]
server-id=1
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
log-error=/var/log/mysql/error.log
pid-file=/var/run/mysqld/mysqld.pid

binlog_expire_logs_seconds=604800

wsrep_provider=/usr/lib/galera4/libgalera_smm.so

wsrep_cluster_address=gcomm://192.168.34.30,192.168.34.29,192.168.34.28

binlog_format=ROW

wsrep_slave_threads=8

wsrep_log_conflicts

innodb_autoinc_lock_mode=2

wsrep_cluster_name=pxc-cluster

wsrep_node_name=koo-controller2
wsrep_node_address=192.168.34.29

pxc_strict_mode=ENFORCING

wsrep_sst_method=xtrabackup-v2

pxc-encrypt-cluster-traffic=OFF

max_connections=20000
bind-address=0.0.0.0
```

# 컨트롤러 1

```/bin/bash
	systemctl start mysql@bootstrap.service
mysql -u root -pIt1

mysql> show status like 'wsrep%';

```

# 컨트롤러 2,3
```/bin/bash
systemctl start mysql
```



[[perconaxtradb 유저 생성]]