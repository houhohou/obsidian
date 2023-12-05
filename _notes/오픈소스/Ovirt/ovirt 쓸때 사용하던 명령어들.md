

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

# 콘솔 리스트
virsh list

# 콘솔 접속
virsh console 8
```

