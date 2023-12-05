#kubernetes 

[[여러가지 서비스들]]

둘다 조건을 저장한 파일을 컨테이너 안에 집어넣는건데, 
secret는 컨테이너 안에서는 확인 할 수없다
configmap은 컨테이너 안에서 확인할 수 있다



아래는 configmap & secret 밸류이다
``` yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  db_host: mongodb-service
---
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
type: Opaque
data:
  username: dXNlcm5hbWU=   #key value
  password: cGFzc3dvcmQ=   #key value    
```



그리고 아래는 mongodb deployment & service이다
``` yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:                                #------------
      - name: mongo-express                      #
        image: mongo-express                     # 컨테이너 생성
        ports:                                   #
        - containerPort: 8081                    #------------
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME  #------------
          valueFrom:                             #
            secretKeyRef:                        # secret value
              name: mongodb-secret               # key: username
              key: username                      #------------
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD  #------------
          valueFrom:                             #
            secretKeyRef:                        # secret value
              name: mongodb-secret               # key: password
              key: password                      #------------
        - name: ME_CONFIG_MONGODB_SERVER         #------------
          valueFrom:                             #
            configMapKeyRef:                     # configmap value
              name: mongodb-configmap            # 
              key: db_host                       #------------
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
---
```



mosquitto 메시지 브로커

configmap & secret

``` yaml
---

apiVersion: v1
kind: ConfigMap
metadata:
  name: mosquitto-config-file
data:
  mosquitto.conf: |
    log_dest stdout
    log_type all
    log_timestamp true
    listener 9001

---

apiVersion: v1
kind: Secret
metadata:
  name: mosquitto-secret-file
type: Opaque
data:
  secret.file: | 
    c29tZXN1cGVyc2VjcmV0IGZpbGUgY29udGVudHMgbm9ib2R5IHNob3VsZCBzZWU= #base64
    
---


``` yaml
---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mosquitto
  labels:
    app: mosquitto
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mosquitto
  template:
    metadata:
      labels:
        app: mosquitto
    spec:
      containers:
        - name: mosquitto
          image: eclipse-mosquitto:1.6.2
          ports:
            - containerPort: 1883
          volumeMounts:                           #-------------------
            - name: mosquitto-conf                #
              mountPath: /mosquitto/config        #
            - name: mosquitto-secret              #
              mountPath: /mosquitto/secret        #
              readOnly: true                      #-------------------
      volumes:
        - name: mosquitto-conf                    #-------------------
          configMap:                              #
            name: mosquitto-config-file           # configmap &   
        - name: mosquitto-secret                  # secret value
          secret:                                 #
            secretName: mosquitto-secret-file     #-------------------

---

```


생성하고 컨테이너안에 들어가서 보면 
시크릿은 아래처럼 보이지않는다

![](https://i.imgur.com/FasblNS.png)


configmap은 아래처럼 configmap이 저장된다
![](https://i.imgur.com/4doaq1R.png)
