#kubernetes 
[[kubernetes]]에서 Service Mesh 를 담당하는 오픈소스

$Service Mesh$
 - `서비스간의 통신을 제어`하고 관리할 수 있도록 하는데 특화된 마이크로서비스를 위한 인프라 계층
 - Proxy를 통해 호출이 이루어지는데, sidecar 라고 불리는 컨테이너를 따로 실행해 proxy를 운영

## 기본구조
![](https://i.imgur.com/FIKNpKj.png)


## 기능
#### 트래픽 통제
**트래픽 분할**
 - 서로 다른 버전을 배포해놓고 버전별로 트래픽 양을 조절할 수 있는 기능
![](https://i.imgur.com/LKZgR8N.png)
그림) 새 버전 테스트용으로 95%(기존) 5%(새 버전) 이런식으로 테스트 가능 

**트래픽 기반의 트래픽 분할**
 - 단순하게 커넥션 기반이 아닌, 네트워크 패킷의 내용을 기반으로 라우팅가능
 ![](https://i.imgur.com/2RNGTa9.png)
그림) HTTP 헤더의(Android, iPhone)종류에 따라 라우팅 가능
#### 서비스간 안정성 제공
**헬스체크 및 서비스 디스커버리**
 - 대상 서비스가 여러개이면 로드밸런싱하고, 주기적으로 상태체크해서 장애가 나면 자동으로 서비스에서 제거한다
![](https://i.imgur.com/mG7aHLY.png)
그림) Pilot이 트래픽 통제를 통해 서비스 호출에 대한 안정성을 제공

**Retry, Timeout, Circuit breaker** 
 - 서비스간의 호출 안정성을 위해서 , 재시도 횟수를 통제할 수 있다. 
 - 호출했을때, 일정시간 이상 응답이 오지않으면 에러처리를 할 수 있고, 
#### 보안
**통신 보안**
 - 기본적으로 envoy를 통해서 통신하는 모든 트래픽을 자동으로 TLS를 이용해서 암호화
![](https://i.imgur.com/4J1DhRc.png)

**서비스 인증과 인가**
 - Istio는 서비스에 대한 인증 (Authentication)을 제공하는데, 크게 서비스와 서비스간 호출에서, 서비스를 인증하는 기능과, 서비스를 호출하는 클라이언트를 직접 인증 할 수 있다.
**서비스간 인증**
- 서비스간 인증은 인증서를 이용하여 양방향 TLS (Mutual TLS) 인증을 이용하여, 서비스가 서로를 식별하고 인증한다.
**서비스와 사용자 인증**
- 서비스간 인증뿐 아니라, 엔드 유저 즉 사용자 클라이언트를 인증할 수 있는 기능인데, JWT 토큰을 이용해서 서비스에 접근할 수 있는 클라이언트를 인증할 수 있다.
**인가를 통한 권한 통제**
- 인증뿐만 아니라, 서비스에 대한 접근 권한을 통제 (Authorization)이 가능하다.
- 기본적으로 역할 기반의 권한 인증 (RBAC : Role based authorization control)을 지원한다.
- 앞에서 인증된 사용자(End User)나 서비스는 각각 사용자 계정이나 쿠버네티스의 서비스 어카운트로 계정이 정의 되고, 이 계정에 역할(Role)을 부여해서 역할을 기반으로 서비스 접근 권한을 정의할 수 있다.
#### 모니터링



## 설치

1. istio 설치
- [b] istio-base 설정
```bash

# 레포추가
helm repo add istio https://istio-release.storage.googleapis.com/charts

# 레포 확인
helm repo list
ig
NAME                	URL
bitnami             	https://charts.bitnami.com/bitnami
grafana             	https://grafana.github.io/helm-charts
kubernetes-dashboard	https://kubernetes.github.io/dashboard/
prometheus-community	https://prometheus-community.github.io/helm-charts
fluent              	https://fluent.github.io/helm-charts
elastic             	https://helm.elastic.co
gitea-charts        	https://dl.gitea.com/charts/
argo                	https://argoproj.github.io/argo-helm
istio               	https://istio-release.storage.googleapis.com/charts

# repo 가져오기
helm  pull istio/base
helm  pull istio/istiod
helm  pull istio/gateway

# 확인
ls
base-1.20.1.tgz    gateway-1.20.1.tgz istiod-1.20.1.tgz

# 압축 풀기
tar -xvf base-1.20.1.tgz 
tar -xvf gateway-1.20.1.tgz
tar -xvf istiod-1.20.1.tgz

# 네임스페이스 생성
kubectl create ns istio-system
```

- [b] yaml파일 설정 
```bash
vi base/values.yaml
global:

  # ImagePullSecrets for control plane ServiceAccount, list of secrets in the same namespace
  # to use for pulling any images in pods that reference this ServiceAccount.
  # Must be set for any cluster configured with private docker registry.
  imagePullSecrets: []

  # Used to locate istiod.
  istioNamespace: istio-system

  istiod:
    enableAnalysis: true

  configValidation: true
  externalIstiod: false
  remotePilotAddress: ""

  # Platform where Istio is deployed. Possible values are: "openshift", "gcp".
  # An empty value means it is a vanilla Kubernetes distribution, therefore no special
  # treatment will be considered.
  platform: ""

  # Setup how istiod Service is configured. See https://kubernetes.io/docs/concepts/services-networking/dual-stack/#services
  # This is intended only for use with external istiod.
  ipFamilyPolicy: ""
  ipFamilies: []

base:
  # Used for helm2 to add the CRDs to templates.
  enableCRDTemplates: false

  # Validation webhook configuration url
  # For example: https://$remotePilotAddress:15017/validate
  validationURL: ""

  # For istioctl usage to disable istio config crds in base
  enableIstioConfigCRDs: true

defaultRevision: "default"
```

- [b] istio-base 설치
```bash
helm install istio-base ./base -f ./base/values.yaml -n istio-system

NAME: istio-base
LAST DEPLOYED: Tue Jan  2 15:09:55 2024
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Istio base successfully installed!

To learn more about the release, try:
  $ helm status istio-base
  $ helm get all istio-base
```


2. istio설치

- [b] istio설정
```bash
vi istiod/values.yaml
# 수정할거 236번줄 hub: 수정해서 바라보는 이미지 레포 수정
# 301번줄 proxy: image:  바라보는 이미지 레포 수정
# 380번줄 proxy_init: image:  바라보는 이미지 레포 수정

# 설치
helm install istio ./istiod -f ./istiod/values.yaml -n istio-system
NAME: istio
LAST DEPLOYED: Tue Jan  2 15:52:55 2024
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
"istiod" successfully installed!

To learn more about the release, try:
  $ helm status istio
  $ helm get all istio

Next steps:
  * Deploy a Gateway: https://istio.io/latest/docs/setup/additional-setup/gateway/
  * Try out our tasks to get started on common configurations:
    * https://istio.io/latest/docs/tasks/traffic-management
    * https://istio.io/latest/docs/tasks/security/
    * https://istio.io/latest/docs/tasks/policy-enforcement/
  * Review the list of actively supported releases, CVE publications and our hardening guide:
    * https://istio.io/latest/docs/releases/supported-releases/
    * https://istio.io/latest/news/security/
    * https://istio.io/latest/docs/ops/best-practices/security/

For further documentation see https://istio.io website
```

3. Istio Ingress Gateway 설치
```bash
vi gateway/values.yaml

# 설치
helm install istio-ingress ./gateway/ -f ./gateway/values.yaml -n istio-system

NAME: istio-ingress
LAST DEPLOYED: Tue Jan  2 15:58:01 2024
NAMESPACE: istio-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
"istio-ingress" successfully installed!

To learn more about the release, try:
  $ helm status istio-ingress
  $ helm get all istio-ingress

Next steps:
  * Deploy an HTTP Gateway: https://istio.io/latest/docs/tasks/traffic-management/ingress/ingress-control/
  * Deploy an HTTPS Gateway: https://istio.io/latest/docs/tasks/traffic-management/ingress/secure-ingress/
```

확인
```bash
kubectl get po -n istio-system
NAME                             READY   STATUS    RESTARTS   AGE
istio-ingress-7c9c597ccb-5cphl   1/1     Running   0          33s
istiod-6d6b7c5957-hgknp          1/1     Running   0          5m37s

kubectl get svc -n istio-system
NAME            TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                      AGE
istio-ingress   LoadBalancer   10.233.6.174    192.168.0.241   15021:30665/TCP,80:31758/TCP,443:32180/TCP   55s
istiod          ClusterIP      10.233.10.101   <none>          15010/TCP,15012/TCP,443/TCP,15014/TCP        5m59s
```




https://velog.io/@beryl/Istio-%EA%B0%9C%EB%85%90