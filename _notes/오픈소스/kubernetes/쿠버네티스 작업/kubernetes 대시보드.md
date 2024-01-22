#kubernetes 

[[kubernetes]] 클러스터를 위한 범용 웹 기반 UI
이를 통해 사용자는 클러스터에서 실행 중인 애플리케이션을 관리하고 문제를 해결할 수 있을 뿐만 아니라 클러스터 자체를 관리할 수 있다.

## 설치방법 

#### yaml파일로 설치

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml --dry-run=client -o yaml > kubernetes-dashboard.yaml

kubectl apply -f kubernetes-dashboard.yaml
```

#### helm으로 설치
```bash
# 레포 추가
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/

# 레포정보 확인
helm repo list

# 파일 다운로드
helm pull kubernetes-dashboard/kubernetes-dashboard --untar

# 패키지 배포
helm install kubernetes-dashboard ./kubernetes-dashboard -n kube-system

# kube-system에 비치시킬 것이고 서비스타입을 바꿈 

# 설치 확인
helm list -n kube-system

# 파라미터 수정
vi kubernetes-dashboard/dashboard-pram.yaml
service:
    type: LoadBalancer

# 파일 설정을 하여 업그레이드      
helm upgrade kubernetes-dashboard ./kubernetes-dashboard -n kube-system -f ./kubernetes-dashboard/dashboard-pram.yaml  

# 설치 확인
helm list -A

# 로드 밸런서로 바뀐 것을 확인
kubectl get svc -n kube-system
```
![](https://i.imgur.com/UacSkef.png)


masterIP : 192.168.34.46
그래서 아래로 접속해보면

192.168.34.46:30163
![](https://i.imgur.com/vjgaOKB.png)

이렇게 뜨는데, 클러스터 롤바인딩하고  토큰 만들어줘야한다.

```bash
# sa계정 확인
kubectl get sa -n kube-system kubernetes-dashboard

#clusterrolebinding 만들기
vi ./kubernetes-dashboard/clusterrolebinding.yaml

kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kubernetes-dashboard
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
  apiGroup: 
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io

# 클러스터 롤바인딩 생성
kubectl create -f ./kubernetes-dashboard/clusterrolebinding.yaml

# 토큰 재생성
kubectl create token -n kube-system kubernetes-dashboard
# 토큰 생성 후 재인증해서 들어가면 정상적으로 실행되는 것을 볼 수 있다.

```

![](https://i.imgur.com/i5jckY9.png)




출처
https://velog.io/@hsshin0602/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-HELM


rocky 8.8 dvd 버전
rocky 9.2 dvd 버전