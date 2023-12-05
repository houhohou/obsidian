#openstack 
[[perconaXtradb]]

셋다 부팅했을때, 안되면 아래 확인해봐서

```/bin/bash
cat /var/lib/mysql/grastate.dat

```
아래처럼 safe_to_bootstrap: 1 이 있는곳이 마스터가됨
![](https://i.imgur.com/DWl8yAe.png)
```/bin/bash
systemctl start mysql@bootstrap.service
```

치면 될거임


