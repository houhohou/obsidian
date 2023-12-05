
[[ubuntu]]에서 [[네트워크]]세팅하는 방법

```
kubectl taint nodes node2 node.kubernetes.io/out-of-service=nodeshutdown:NoExecute
```


```bash

vi /etc/netplan/

# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      addresses:
      - 192.168.34.226/24
      gateway4: 192.168.34.1
      nameservers:
        addresses:
        - 8.8.8.8
        - 8.8.4.4
    ens35:
      addresses:
      - 10.185.214.252/24
  version: 2
```

