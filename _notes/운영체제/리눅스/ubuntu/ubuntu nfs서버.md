#리눅스 
[[ubuntu]]서버에 nfs 설정하는법



먼저 nfs host 서버
```bash
sudo apt install nfs-kernel-server
mkdir -p /nfs

cat >> /etc/exports << E0F
/nfs 192.168.34.0/24(rw,sync,no_root_squash)
E0F

chmod 777 /nfs
systemctl restart nfs-server
```

nfs 클라이언트 
```bash
sudo apt install -y nfs-common
mkdir -p /nfs
mount 192.168.34.46:/nfs /nfs
```


