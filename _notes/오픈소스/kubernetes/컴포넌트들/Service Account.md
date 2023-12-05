#kubernetes 
[[kubernetes]]에서 실행되는 [[Pod]]에 자격부여

✅ 쿠버네티스의 계정에는 2종류(유저 어카운트, 서비스 어카운트)가 존재한다
- 유저 어카운트 : 운영자 또는 개발자 
- 서비스 어카운트 : 프로메테우스, 젠킨스 같은 서비스가 사용
		[[Pod]]가 [[kubernetes API]]와 상호작용하기 위해 접근권한이 필요 (보안이슈 최소화 하기 위해 존재)
##### 특징
👉 [[namespace]] 종속
👉 서비스 어카운트 생성 후 전용 토큰을 생성해야함
👉 모든 네임스페이스에는 default 서비스 어카운트가 존재하며, 서비스 어카운트를 별도로 지정하지 않는 경우 자동으로 default 서비스 어카운트를 사용

##### service Account 주요 요소
✅ Service Account admission 컨트롤러
	👉 sa를 지정하지 않은 pod에 'default' sa를 할당
✅ Service Account Token 컨트롤러
	👉 클러스터의 각 sa에 대한토큰 생성 관리를 담당
✅ Service Account 컨트롤러
	👉 sa 와 sa관련 secret생성 및 삭제 관리
✔️ [[namespace]]에 기본생성되는  service account
```bash
$ k get sa
NAME      SECRETS   AGE
default   0         14d
```



✅ 기본 이 세가지를 pod에 할당한다. 
![](https://i.imgur.com/yUllRBp.png)
👉 ca.crt ( 공개키 )
- kubernetes 클러스터에 대한 인증기관(CA)인증서가 포함 ( 클라이언트 - API 통신인증 )
👉 namespace
- service account가 [[namespace]]종속이라서 namespace를 명시하기 위해 
👉 token
- 서비스 계정을 대신해서 API서버에 요청을 인증하는데 사용 ( ca.crt를 사용해 확인가능)





## 실습

service account 생성하기
```bash
k create sa [서비스어카운트 이름]
k creat token [서비스어카운트_이름]
```

✅ sa가 적용된 deploy 설정
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      serviceAccountName: samplesa     # service account 지정 
      containers:
      - name: my-container
        image: nginx:1.14.2
```

위를 적용시켜보면 
Pod 에 Service Account 가 마운트된다.
```bash
k apply -f deploysa.yaml

k describe pod [파드이름]
```
![](https://i.imgur.com/L2S1vOp.png)

파드에 접속해보면  API 서버와 통신할 수 있는 Token값들이 있다.
![](https://i.imgur.com/SjVln9p.png)

serviceaccount에게 권한을 주기위해 Role & Rolebinding만들기
✔️ 아래 Role 권한 : pods 에 대해 ["get", "watch", "list"] 가능
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: service-account-example-role
  namespace: default
rules:
  - apiGroups: [""] 
    resources: ["pods"]
    verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: service-account-example-role-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: samplesa 
    namespace: default
roleRef:
  kind: Role 
  name: service-account-example-role 
  apiGroup: rbac.authorization.k8s.io
```







실습출처
https://kingofbackend.tistory.com/237