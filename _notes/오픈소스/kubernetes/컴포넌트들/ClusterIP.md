#kubernetes

[[kubernetes]]에서 [[Pod]]들을 묶어서 내부에서 사용할 수 있게 하나의 IP로 제공

'app: my-app' label이 있는 [[Pod]]를 선택해서 
80/tcp 에서 사용할 수 있게 한다. 
``` yaml
apiVersion: v1
kind: Service
metadata:
  name: my-clusterip-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```
