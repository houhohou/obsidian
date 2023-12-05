
[[웹서버]]와 [[브라우저]] 간에 [[암호화]]된 링크를 설정하기 위한 표준 [[보안]] 기술
#### cfssl사용 
RootCA
Server CA
ClientCA
생성

cfssl 설치
```bash
apt install golang-cfssl -y
```
##### Root CA생성
디렉터리 생성
```bash
mkdir auth
cd auth/
```

인증서 생성에 필요한 rootCA config 파일
```bash
cat > rootCA-config.json <<EOF
{
  "signing": {
    "default": {
      "expiry": "8760h"
    },
    "profiles": {
      "root-ca": {
        "usages": ["signing", "key encipherment", "server auth", "client auth"],
        "expiry": "8760h"
      }
    }
  }
}
EOF
```


rootCA 인증서 서명 요청 json
```bash
cat > rootCA-csr.json <<EOF
{
  "CN": "rootCA",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "O": "Kubernetes"
    }
  ]
}
EOF
```

Root CA 생성
```bash
cfssl gencert -initca rootCA-csr.json | cfssljson -bare rootCA
```
![](https://i.imgur.com/NyNDXaG.png)


```bash
ll
```
![](https://i.imgur.com/BuxhSLg.png)


##### Server 인증서 생성

IP 확인
```bash
curl ifconfig.co
```
![](https://i.imgur.com/4z8cLQB.png)



```bash
cat > server-csr.json <<EOF
{
  "CN": "localhost",
  "hosts": [
    "localhost",
    "112.221.20.133"
  ],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "O": "server-group"
    }
  ]
}
EOF
```


server 인증서 생성
```bash
cfssl gencert -ca=rootCA.pem -ca-key=rootCA-key.pem -config=rootCA-config.json   -profile=root-ca   server-csr.json | cfssljson -bare server
```
![](https://i.imgur.com/GC1lQOA.png)

```bash
ll
```
![](https://i.imgur.com/VOSpHm5.png)



```bash
cat > client-csr.json <<EOF
{
  "CN": "localhost",
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "names": [
    {
      "O": "client-group"
    }
  ]
}
```

```bash
cfssl gencert   -ca=rootCA.pem   -ca-key=rootCA-key.pem   -config=rootCA-config.json   -profile=root-ca   client-csr.json | cfssljson -bare client
```

```/bin/bash
ll
```
![](https://i.imgur.com/pn0CBYX.png)

