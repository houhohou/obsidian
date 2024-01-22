#kubernetes 
[[kubernetes]] 를 [[오픈소스/git]]과 연동하여 commit하면 자동으로 컴포넌트로 올려준다. 



##### 설치
👉argocd 설치
```bash
kubectl create namespace argocd 
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml --dry-run=client -o yaml >> argocd.yaml

# 타입 loadbalancer로 변경 후 포트 확인후 접속
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

👉helm으로 설치
```bash
# 레포추가
helm repo add argo https://argoproj.github.io/argo-helm

# values.yaml 파일가져오기
helm show values argo/argo-cd > values.yaml

# values.yaml 변경
server.insecure: true. # false -> true

# 네임스페스 생성
kubectl create namespace argocd

# helm에서 실행
helm install -n argocd argocd argo/argo-cd -f values.yaml

```

👉argocd CLI 설치
```bash
# CLI 설치
brew install argocd

# Password 생성
argocd admin initial-password -n argocd

#Password 확인
kubectl get secret argocd-initial-admin-secret -o yaml
...
data:
  password: VEpCNllILTNLdVZmZ2szUg==     #base64로 인코딩 되어있어서 디코딩하면됨
...
```

👉[[Base64]] 디코딩 python 코드
```python
import base64

# 인코딩된 문자열
encoded_string = input("여기에 Base64로 인코딩된 문자열을 입력하세요: ")

# Base64로부터 디코딩
decoded_bytes = base64.b64decode(encoded_string)

# 디코딩된 데이터를 문자열로 변환 (UTF-8 인코딩 가정)
decoded_string = decoded_bytes.decode('utf-8')

print(decoded_string)
```


## 시작하기

새로운 App를 생성해보기 위해 
![600](https://i.imgur.com/1MFRep1.png)



![600](https://i.imgur.com/KRpmUTw.png)

✅ 옵션 설명
- Application Name: App의 이름을 적습니다.
- Project: 프로젝트를 선택하는 필드입니다. 쿠버네티스의 namespace와 비슷한 개념으로 여러 App을 논리적인 project로 구분하여 관리할 수 있습니다.
- Sync Policy: Git 저장소의 변경 사항을 어떻게 sync할지 결정합니다. 
	👉Autosync :  자동으로 Git 저장소의 변경사항을 운영에 반영
	👉Manual : 사용자가 버튼 클릭을 통해 직접 운영 반영
- Repository URL: ArgoCD가 바라볼 Git 저장소를 의미합니다.
- Revision: Git의 어떤 revision (HEAD, master branch 등)을 바라 볼지 결정합니다.
- Path: Git 저장소에서 어떤 디렉토리를 바라 볼지 결정합니다. (dot(.)인 경우 root path를, 디렉토리 이름을 적으면 해당 디렉토리의 배포 정의서만 tracking 합니다.)
- Cluster: 쿠버네티스의 어느 클러스터에 배포할지를 결정합니다.
- Namespace: 쿠버네티스 클러스터의 어느 네임스페이스에 배포할지를 결정합니다.
- Directory Recurse: path아래의 디렉토리를 재귀적으로 모니터링하여 변경 사항을 반영합니다.


### 실습
내 깃허브에 
https://github.com/houhohou/argocd_test.git

![200](https://i.imgur.com/KtIoJgH.png)



- Application Name: gitops-argocd
- Project: default
- Sync Policy: manual
- Repository URL: https://github.com/houhohou/argocd_test.git
- Revision: HEAD
- Path: .
- Cluster: in-cluster
- Namespace: default
- Directory Recurse: Unchecked

하고 create

SYNC APPS누르고 좀 기다리면

![200](https://i.imgur.com/W3yjnHx.png)


Healthy눌러보면 아래처럼 뜬다. 

![](https://i.imgur.com/aUKCDs5.png)

확인해보면 올라왔지만, 접속할수없다
![](https://i.imgur.com/J9MDAGh.png)


```bash
# git 불러오기
git clone https://github.com/houhohou/argocd_test.git
cd argocd_test

# type: Loadbalancer로 변경
vi svc.yaml
...
spec:
  type: LoadBalancer
  ports:
...

# git commit
git add svc.yaml
git commit -m "type: Loadbalancer"
git push origin main
```


그럼 아래처럼 노란불이 뜬다.
![](https://i.imgur.com/25FPnSD.png)


아래보면 Loadbalancer가 잘 떠있다. 
![](https://i.imgur.com/CdUaxGc.png)


접속이 된다!
![500](https://i.imgur.com/jaFqGpj.png)




✅ Argocd 를 다른 네임스페이스에서 ingress접속하게 하는방법
[[ExternalName]]을 사용한다. 
```bash
# externalname생성 ( 네임스페이스 넘어서 엔드포인트 생성 )
cat >> external-argo.yaml << E0F
apiVersion: v1
kind: Service
metadata:
  name: argocd-svc-external
  namespace: argocd
spec:
  type: ExternalName
  externalName: argocd-server.argocd.svc.cluster.local
  ports:
  - port: 80
E0F

# 실행
kubectl apply -f external-argo.yaml


# 잘 받아오는지 테스트하기위해 pod 생성 후 접속
kubectl run -n default --image=busybox:1.28 --restart=Never --rm -i testpod --command -- sh

# DNS조회 수행
nslookup argocd-svc-external.default.svc.cluster.local
...
Server:    169.254.25.10
Address 1: 169.254.25.10

Name:      argocd-svc-external.default.svc.cluster.local
Address 1: 10.233.25.158 argocd-server.argocd.svc.cluster.local
...


```
