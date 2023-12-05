#kubernetes

[[kubernetes]]에서 [[Pod]]들이 노드를 통해 포트로 나가게함


내부적으로 ClusterIP서비스를 생성해서 이를 각 노드의 포트 30007에 노출한다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
      nodePort: 30007
```

