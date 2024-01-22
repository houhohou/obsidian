#kubernetes 
[[kubernetes]] 에서 두가지 타입으로 public에서 접근이 가능한데, 하나는 [[LoadBalancer]] 그리고 또 하나가 ingress이다. [[LoadBalancer]]를 선택하면 서비스에도 외부 ip가 생기지만, 서비스가 많아지면, 엔드포인트도 한계가 있기에, ingress와 ingress controller가 생겼다. 


아래처럼 하나의 엔드포인트를 생성해 host 헤더에 정보만 추가해서 변경
![](https://i.imgur.com/LNYUSvC.png)

여기에 ingress만으로는 충족할수없는데 
**ingress** : 라우팅의 내용을 명세
**ingress controller** : 직접적인 ingress의 동작을 시행 



### ingress 설치하기

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.6.4/deploy/static/provider/cloud/deploy.yaml
```

기본 ingress
``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/ingress.class: "nginx" # 이게 있어야 nginx 인그레스 컨트롤러 씀
spec:
  rules:
  - host: dashboard.com      # host를 명시안하면 IP로 연결 ( 주석처리 )
    http:
      paths:
      - path: /
        pathType: Exact  
        backend:
          service:
            name: kubernetes-dashboard
            port: 
              number: 80
```


### yaml 의미
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```
👉`ImplementationSpecific` : Ingress 컨트롤러에 의존하여 경로 매칭 방식을 결정
👉`rewrite-target 주석`: annotation은 특정 리소스에 사용자가 원하는 특정행동을 하도록 만들기 위해 사용됩니다. rewrite-target은 인그레스에 정의된 경로로 들어오는 요청을 설정된 경로로 전달합니다.  
    위의 예시에서는 path값인 /testpath로 들어오는 모든 요청을 test이름의 service의 /로 전달하겠다는 의미입니다.  
    /testpath/test -> test  
    /testpath/test/foo -> /test/foo  
    /testpath -> /
👉`ingressClassName`: ingress controller가 한 클러스터 내에서 여러개가 존재할 때, 어떤 ingress controller를 사용할 지 ingres class 리소스를 생성해서 정의할 수 있습니다. volume에서 배웠던 storageClass라고 생각하시면 되겠습니다.
👉`spec.rules`와 `spec.http` 사이에 `spec.host`: 어떤 hostName으로 ingress에 접근할 것인지 명세. 비어있으면 hostName이 아닌, ip와 엔드포인트로 접근하겠다는 뜻입니다.
👉`path`: 엔드포인트를 의미합니다.
👉`pathType`: 엔드포인트의 필터링 기준

| 종류 | 입력 | 허용 |
|------|------|-----| 
| Exact | /test /test/ | /test만 적용 |
| Prefix | /test /test/ | 둘다 적용 |
👉`ImplementationSpectific`: IngressClass에서 정의한 값을 따라서 routing이 되는 방식으로, 아무 설정 안했을 때 default로 동작한다. IngressClass의 정의에 따라서 달라질 수 있다.
### 접속방법
```bash
# ingress-controller loadbalancer port확인하기
kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.233.20.40    192.168.0.240   80:30362/TCP,443:30783/TCP   108m
ingress-nginx-controller-admission   ClusterIP      10.233.31.203   <none>          443/TCP                      108m

# 접속 
<master IP>:30362
```


아래 ingress 로 만들면
접속할때, 
sangm.com:(ingress-controller port)
sangm.com:(ingress-controller port)/test
로 접근가능
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: sangm.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: second-service # 이 이름으로 된 service를 찾아 바운드함
            port:
              number: 80
      - path: /test
        pathType: Exact
        backend:
          service:
            name: my-service  # 이 이름으로 된 service를 찾아 바운드함
            port:
              number: 80
```

❗path: / 로 한 부분에 Prefix 말고 Exact 하니 접근안되던오류가 있음


## 다른 namespace에 있는 ingress 연동

아래처럼 externalname을 추가해서 
```bash
apiVersion: v1
kind: Service
metadata:
  name: argocd-svc-external # ingress에 명시할 이름
  namespace: default
spec:
  type: ExternalName
  externalName: argocd-server.argocd.svc.cluster.local # svc이름.네임스페이스
  ports:
  - port: 80
```

ingress에 붙이면 됨
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: default
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: sangm.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: second-service
            port:
              number: 80
      - path: /test
        pathType: Exact
        backend:
          service:
            name: my-service
            port:
              number: 80
      - path: /argo
        pathType: Prefix
        backend:
          service:
            name: argocd-svc-external
            port:
              number: 80
```

## ingress에 https구현하기

``` yaml
# 아래와같이 tls키를 만들어준다. 
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=sangm.com/O=sangm.com"

# 인증서, 비밀키 인코딩
cat tls.key | base64 | tr -d '\n'
cat tls.crt | base64 | tr -d '\n'

cat >> secret-ingress.yaml << E0F
apiVersion: v1
kind: Secret
metadata:
  name: tls-secret
  namespace: default
type: kubernetes.io/tls
data:
  tls.key: <인코딩된 .key>
  tls.crt: <인코딩된 .crt>
E0F

# secret 생성
kubectl apply -f secret-ingress.yaml


# yaml파일에 아래와 같이 추가:ㅃ1
...
spec:
  tls:
  - hosts:
    - sangm.com
    secretName: tls-secret
  rules:
...
# 443포트 확인
kubectl get svc -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.233.20.40    192.168.0.240   80:30362/TCP,443:30783/TCP   18h
ingress-nginx-controller-admission   ClusterIP      10.233.31.203   <none>          443/TCP                      18h

# 접속확인
curl -k -v https://sangm.com:30783
```





![[Pasted image 20231029001543.png]]