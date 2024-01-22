

multipath -ll

```bash
vi /etc/multipath.conf
defaults {
  user_friendly_names no
  flush_on_last_del yes
  skip_kpartx yes
  remove_retries 10
}
```


- [8] virsh 명령어
```bash
# 인증 안하고 들어가기
virsh -c qemu:///system?authfile=/etc/ovirt-hosted-engine/virsh_auth.conf

# 콘솔 바로 접속
virsh -c qemu+ssh://root@<KVM-HYP-NAME>/system

# 콘솔 리스트
virsh list

# 콘솔 접속
virsh console 8

<serial type='pty'>
  <target port='0'/>
</serial>
<console type='pty'>
  <target type='serial' port='0'/>
</console>
```

![[Pasted image 20231205165710.png]]

![[Pasted image 20231205170030.png]]


혹시나 san 스토리지 뜨지않고 
![](https://i.imgur.com/HMaKrqD.png)
위처럼 뜬다면 아래처럼 스토리지 찌꺼기 제거해보자
```
fdisk -l
lsblk
# 위 명령어로 확인

# 아래 명령어로 찌꺼기 제거
dd if=/dev/sdc of=/dev/mapper/36006016028b15900ef2d4c65b38f05d5 bs=1M count=1
wipefs -a /dev/sdc --force
```