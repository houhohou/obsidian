#kubernetes

내부서비스들이 외부의 DNS이름인  'my.database.example.com'로 정의된 레코드값을 반환하게함

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-externalname-service
spec:
  type: ExternalName
  externalName: my.database.example.com
```