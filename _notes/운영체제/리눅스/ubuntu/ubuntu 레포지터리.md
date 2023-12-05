[[ubuntu]]기반 [[repo]]형성하는 방법


경로안에
dists/
pool/
두개 있어야함

보통 
```

deb http://kr.archive.ubuntu.com/ubuntu jammy main restricted 
deb http://kr.archive.ubuntu.com/ubuntu jammy-updates main restricted 
deb http://kr.archive.ubuntu.com/ubuntu jammy universe
deb http://kr.archive.ubuntu.com/ubuntu jammy-updates universe
deb http://kr.archive.ubuntu.com/ubuntu jammy multiverse
deb http://kr.archive.ubuntu.com/ubuntu jammy-updates multiverse
deb http://kr.archive.ubuntu.com/ubuntu jammy-backports main restricted universe multiverse
deb http://kr.archive.ubuntu.com/ubuntu jammy-security main restricted
deb http://kr.archive.ubuntu.com/ubuntu jammy-security universe
deb http://kr.archive.ubuntu.com/ubuntu jammy-security multiverse
```


이렇게 되어있는데, 위의 상태는 아래처럼 나온다
![](https://i.imgur.com/6VYUNkd.png)


![](https://i.imgur.com/gQps3pp.png)


![](https://i.imgur.com/7Rnr53v.png)


sudo add-apt-repository 'deb 101.202.40.5/hana-ubuntu/mirror/kr.archive.ubuntu.com/percona/mirror/repo.percona.com/pxc-80/apt jammy main'
