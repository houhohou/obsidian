---
sticker: ui//mk-ui-collapse-sm
---
#kubernetes 
[[kubernetes]]에서 [[pod]]들을 관리하기 위해 존재

👉 deployment에서 자식 [[pod]]들을 설정하기 위해 레이블로 관리하는데
```bash
# deploy.yaml에 설정할때 label을 지정해줘야한다. 
app: nginx #수동지정한 label

# 아래는 label을 지정하지 않아도 deploy를 구분하기 위해 자동 생성되는 label이다 
pod-template-hash: [고유번호]

# deploy를 확인해보면 match label이 두개이다. 
NAME                              READY   STATUS    RESTARTS   AGE   LABELS
nginx-deploy01-85996f8dbd-87wwt   1/1     Running   0          11m   app=nginx,pod-template-hash=85996f8dbd
nginx-deploy01-85996f8dbd-dvhrl   1/1     Running   0          11m   app=nginx,pod-template-hash=85996f8dbd
nginx-deploy01-85996f8dbd-zlnsq   1/1     Running   0          11m   app=nginx,pod-template-hash=85996f8dbd

# 만약 아래처럼 deploy와 같이 label을 만들게 되면 deploy가 생성된 pod를 지워버린다. 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-deploy01-3
  labels:
    pod-template-hash: 85996f8dbd
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

✅ 기본 deploymemt
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
---
```

