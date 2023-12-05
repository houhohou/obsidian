#kubernetes 
[[kubernetes]]에서는 태그를 붙일 수 있는데 두가지 종류가 있다


👍기본 표시 방법
**키: 값**

✅ **어노테이션** : 시스템 구성요소가 사용
- 시스템 구성 요소를 위한 데이터 저장   
- 모든 환경에서 사용할 수 없는 설정
- 정식으로 통합되기 전의 기능을 설정


✅ **레이블** : 리소스 관리에 사용
- 개발자가 사용하는 레이블
- 시스템이 사용하는 레이블

##### 어노테이션
👉annotation의 예
```bash
---
apiVersion: v1
kind: Pod
metadata:
  name: sample-annotations
  annotations:
    annotation1: val1
    annotation2: "200"
spec:
  containers:
  - name: nginx-container
    image: nginx:1.16
---
```
##### 레이블
👉label의 예
```bash
---
apiVersion: v1
kind: Pod
metadata:
  name: sample-label
  labels:
    label1: val1
    label2: val2
spec:
  containers:
  - name: nginx-container
    image: nginx:1.16
---
```

##### 레이블 별로 확인할때
```bash
# Pod 표시할때 레이블까지 표시
$  k get pods --show-labels
NAME     READY   STATUS    RESTARTS   AGE     LABELS
nginx    1/1     Running   0          27h     app=test01
nginx2   1/1     Running   0          7m9s    app=test02
nginx3   1/1     Running   0          4m49s   app02=testapp01
nginx4   1/1     Running   0          4m49s   app02=testapp02

# -L 옵션 : '값' 말고 '키' 만 찾을때 표기된다, 다른 것도 같이 검색됨
$ k get pods -L app
NAME     READY   STATUS    RESTARTS   AGE   APP
nginx    1/1     Running   0          27h   test01
nginx2   1/1     Running   0          14s   test02

# 레이블 키 값으로 검색2
$ k get pods -L app,app02
NAME     READY   STATUS    RESTARTS   AGE     APP      APP02
nginx    1/1     Running   0          27h     test01
nginx2   1/1     Running   0          3m28s   test02
nginx3   1/1     Running   0          68s              testapp01
nginx4   1/1     Running   0          68s              testapp02

# 키: 값 까지  검색할때
$ k get pods -l app=test01
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          27h

```
 

