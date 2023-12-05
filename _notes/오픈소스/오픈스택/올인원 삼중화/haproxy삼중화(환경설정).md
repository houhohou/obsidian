#openstack 


```/bin/bash
#1,2,3
apt install -y haproxy
cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.bak
```
haproxy pcs리소스 생성
```/bin/bash
#1
pcs resource create haproxy01 systemd:haproxy op monitor interval=10s
```


![](https://i.imgur.com/qHTL2BQ.png)


둘다 공동배치
```/bin/bash
#1
pcs constraint colocation add haproxy01 with vip

```



vip시작후 haproxy시작하게 설정
```/bin/bash
pcs constraint order vip then haproxy01
```

![](https://i.imgur.com/80Svpoi.png)




```/bin/bash
#1,2,3
systemctl restart haproy
```




```/bin/bash


```




```/bin/bash


```




```/bin/bash


```




```/bin/bash


```




```/bin/bash


```




```/bin/bash


```




```/bin/bash


```



