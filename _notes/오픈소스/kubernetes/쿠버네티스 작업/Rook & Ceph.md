#kubernetes 
[[kubernetes]]


## Rook 
여러가지 스토리지 오케스트레이터로 플랫폼 & 프레임워크를 지원
 - Rook에서 제공하는 가상스토리지 대표적인 두가지
	 - EdgeFS: 데이터베이스처럼 대용량 스토리지가 필요할때 사용
	 - Ceph: 공유 확장에 특화되어 있는 스토리지가 필요할때 사용


#### Rook 구성도
쿠버네티스 POD로 실행되고, Ceph나 EdgeFS 등을 POD로 배포해서 관리하는 도구 
![](https://i.imgur.com/hYJsNRj.png)



## Ceph
공유 확장에 특화되어 있는 스토리지
#### Ceph 스토리지 유형
**Block Stroage** : 단일 POD에 storage 제공합니다.
**Object Storage** : 애플리케이션이 쿠버네티스 클러스터 내부 또는 외부에서 액세스 할수있느 데이터를 IO 할수있고, S3 API를 스토리지 클러스터에 노출을 제공합니다.
**Shared Stroage** : 여러 POD에서 공유할 수있는 파일 시스템기반 스토리지입니다.

## 시작하기
```bash
# 깃으로 긁어오기
git clone https://github.com/rook/rook.git

# 들어가보면 다양한 예제가 있다
cd rook/deploy/examples/

# 아래 4개yaml을 설치하면 기본적으로 3개의 디스크를 이용한 rook ceph 클러스터가 구성됨
# crd 생성
kubectl apply -f crds.yaml
# namespace & clusterrole & rolebinding & serviceacount
kubectl apply -f common.yaml
# configmap & deployment
kubectl apply -f operator.yaml
# CephCluster 
kubectl apply -f cluster.yaml


# 
kubectl  apply  -f toolbox.yaml
```


#### Ceph 명령확인하기
```bash
# 툴박스로 셰프 명령어 치기위해 toolbox 띄우기
kubectl  apply  -f toolbox.yaml

# 툴박스 접속방법
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- bash

# 명령어쳐보면
ceph status
```
![300](https://i.imgur.com/e26SKYW.png)

#### wordpress띄워보기
```bash
# 예제 App이 사용할 StorageClass를 생성
kubectl apply -f csi/rbd/storageclass.yaml

# mysql 띄우기
kubectl create -f mysql.yaml

# pvc bound되었는지 확인
kubect get pvc
```
![](https://i.imgur.com/hCyTJrP.png)
```bash
# wordpress 띄우기
kubectl create -f wordpress.yaml

# 확인
kubectl get pods
NAME                                               READY   STATUS
wordpress-5c74fb76c6-mg4lb                         1/1     Running

# 포트 확인
kubectl get svc  wordpress
NAME        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)    
wordpress   LoadBalancer   10.233.59.217   <pending>     80:31210/TCP
```



#### 프로메테우스 모니터링 하기
```bash
# 아래처럼 변경해서 apply
vi cluster.yaml
```
![300](https://i.imgur.com/JEfEhpQ.png)
```bash
# 아래처럼 변경
vi operator.yaml
```
![300](https://i.imgur.com/bkDeLz5.png)





#### 공식홈페이지 사이트
모니터링하는법
https://rook.io/docs/rook/v1.10/Storage-Configuration/Monitoring/ceph-monitoring/#prometheus-instances

대시보드
https://rook.io/docs/rook/v1.10/Storage-Configuration/Monitoring/ceph-dashboard/
