#kubernetes
[[kubernetes]]

외부 도메인으로 cname을 반환한다.

##### 사용용도
👉 클러스터 내부에서 엔드포인트를 쉽게 변경하고싶을경우 않는 사용함
👉 다른 네임스페이스에서 service를 사용하고자 할때 

내부서비스들이 외부의 DNS이름인  'my.database.example.com'로 정의된 레코드값을 반환하게함

```yaml
apiVersion: v1
kind: Service
metadata:
  name: sample-externalname
  namespace: default
spec:
  type: ExternalName
  externalName: my.database.example.com 
```
❗이때 `externalNale`은  {Service Name}.{Namespace}.svc.cluster.local 형식으로 작성


```bash
# 테스트
kubectl run --image=amsy810/tools:v2.0 --restart=Never --rm -i testpod --command -- dig sample-externalname.default.svc.cluster.local CNAME
```