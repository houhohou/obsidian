#openstack 
[[perconaXtradb]]

셋다 부팅했을때, 안되면 아래 확인해봐서

```bash
cat /var/lib/mysql/grastate.dat
```
아래처럼 safe_to_bootstrap: 1 이 있는곳이 마스터가됨
![](https://i.imgur.com/DWl8yAe.png)
```bash
# safe_to_bootstrap: 1 인 노드
systemctl start mysql@bootstrap.service

# safe_to_bootstrap: 0 인 노드
systemctl start mysql
```

치면 될거임


