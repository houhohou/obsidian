#kubernetes 


공홈 레포

## helm 설치
원하는 버전 갖고오기
https://github.com/helm/helm/

포장풀기
tar -zxvf helm-v3.0.0-linux-amd64.tar.gz

heml 이동
mv linux-amd64/helm /usr/local/bin/helm

## helm search검색 명령어
헬름은 강력한 검색 명령어를 제공한다. 서로 다른 2가지 소스 유형을 검색하는데 사용할 수 있다.

- helm search hub는 여러 저장소들에 있는 헬름 차트들을 포괄하는 [헬름 허브](https://artifacthub.io/)를 검색한다.  
    -> 쿠버네티스 패키지(차트)를 찾고 설치할 수 있는 사이트
    
- helm search repo는 helm repo add를 사용하여 로컬 헬름 클라이언트에 추가된 저장소들을 검색한다. 검색은 로컬 데이터 상에서 이루어지며, 퍼블릭 네트워크 접속이 필요하지 않다.





## 레포 추가
👉bitnami 레포 추가
helm repo add bitnami https://charts.bitnami.com/bitnami

추가됐는지확인
helm search repo bitnami


	레포 추가하고 업데이트
```/bin/bash
helm repo update
```

	helm에 추가한 레포지토리 확인
```/bin/bash
helm repo list
```

	helm특정 레포안에 패키지 확인
```/bin/bash
helm search repo bitnami
```

	공개적으로 사용가능한 차트 확인
```/bin/bash
helm search hub nginx
```



	helm으로 설치한 패키지리스트
```/bin/bash
helm list
```

	helm 그라파나 설치(네임스페이스까지 설정)
```/bin/bash
helm install <name> grafana/grafana-agent-operator -n grafana 
```

	지우기
```/bin/bash
helm uninstall my-release
```

## yaml파일로만 받기 
```/bin/bash
helm pull grafana/grafana-agent-operator --untar
```
![](https://i.imgur.com/HhzImJA.png)

yaml파일로 받은거 실행하기 
```/bin/bash
helm install grafana-monitoring ./grafana-agent-operator -n grafana
```



## 설치 작업에 구성 데이터를 전달하는 방법
- --values (또는 -f): 오버라이드(override)할 YAML 파일을 지정한다. 여러 번 지정할 수 있지만 가장 오른쪽에 있는 파일이 우선시된다.
    
- --set: 명령줄 상에서 오버라이드(override)를 지정한다.

둘다 사용하면, --set 값은 더 높은 우선순위를 가진 --values 으로 병합된다. --set에 명시된 오버라이드 사항들은 컨피그맵(ConfigMap)으로 보관된다.


```null
# 필요한 부분만 설정
vi my-pram.yaml
primary:
  service:
    type: LoadBalancer
    
helm install mydb bitnami/mysql -f my-pram.yaml

# 로드밸런스로 되어있는 것을 확인 가능
kubectl get svc


# 직접 설정, 잘 사용하지 않음
helm install mydb bitnami/mysql --set 'primary.service.type=NodePort'
```



## 새로운 차트로 업데이트
업그레이드는 현존하는 릴리스를 가지고, 사용자가 입력한 정보에 따라 업그레이드한다. 쿠버네티스 차트는 크고 복잡할 수 있기 때문에, 헬름은 최소한의 개입으로 업그레이드를 수행하려고 한다. 최근 릴리스 이후로 변경된 것들만 업데이트하게 될 것이다.

```null
# 기존에 그대로 설치
helm install mydb bitnami/mysql


# 업그레이드
helm upgrade mydb bitnami/mysql -f my-pram.yaml

# revision이 2로 되어있고 파일대로 업그레이드 된 것을 볼 수 있다.
helm list 
# 서비스 타입이 로드밸런서임
kuebectl get svc

# 이전 버전 보기
helm history mydb

# 이전 버전으로 돌아기기
helm rollback mydb 1

# revision이 3으로 바뀌고 상태에 이전 상태로 돌아간다.
helm list 
helm history mydb

# 서비스 타입이 ClusterIP임
kuebectl get svc

# 제거작업
helm uninstall mydb
kubectl delete pvc --all
```