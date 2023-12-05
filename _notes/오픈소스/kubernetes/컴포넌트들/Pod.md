#kubernetes
[[kubernetes]]에서 [[컨테이너]]를 관리하는 배포 가능한 가장 작은 단위. 



pod 생성 명령어
```bash
k run t1 --image nginx --dry-run=client
```


### nginx 기본 pod 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

## 기본적인 pod yaml에 대해 설명

**apiVersion** : 쿠버네티스에게 어떤버전의 API를 사용할지 알려줌
pod가 오래도록 존재해와서 지금은 v1에 통합됐지만, 
다른 리소스 유형은 버전에 그룹이 포함될수 있다. 
	ex) Cronjob : batch/v1beta1
	ex) Job : batch/v1
**kind** : 리소스 실제 이름 ( Pod, ConfigMap, CronJob )
**metadata** : 최상위 키값 묶음
**spec** : 리소스 관련 설정을 포함하는 키 



