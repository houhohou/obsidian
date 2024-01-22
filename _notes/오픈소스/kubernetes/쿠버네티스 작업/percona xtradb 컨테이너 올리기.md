
일단 클론해서 가져오기
```bash
git clone -b v1.13.0 https://github.com/percona/percona-xtradb-cluster-operator
cd percona-xtradb-cluster-operator
```


```bash
# 사용자 정의 리소스 정리
kubectl apply -f deploy/crd.yaml

# 네임스페이스 만들기 
kubectl create namespace pxc

# 네임스페이스 변경
kn pxc

# 역할 기반 액세스 제어 만들기 (role & service account & rolebinding)
kubectl apply -f deploy/rbac.yaml

# deployment 시작
kubectl apply -f deploy/operator.yaml

# 비밀번호 생성
kubectl create -f deploy/secrets.yaml

# percona xtradb 클러스터 생성
kubectl apply -f deploy/cr.yaml
```





```bash

```



```bash

```



```bash

```
