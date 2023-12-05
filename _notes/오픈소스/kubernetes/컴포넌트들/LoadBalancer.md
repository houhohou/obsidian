#kubernetes
[[kubernetes]]의 [[Pod]]간에 로드밸런싱 담당

클라우드 공급자의 Loadbalance를 이용해서 서비스를 외부에 노출시키는데 사용. 
외부 IP주소를 할당해서 외부 트래픽이 서비스에 도달하게 할수 있도록함

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```