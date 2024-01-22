#kubernetes

[[kubernetes]]에서  [[Pod]] 세트와 이에 액세스하는 정책을 정의.
label로 선택해서 여러 노드에 걸쳐서 [[Pod]]집합에 고정 IP와 로드밸런싱을 제공
L4단의 접근이라서 각 service에서 ip와 port번호를 노출시키게 된다. 

<!--⚠️Imgur upload failed, check dev console-->
![[Pasted image 20231226160720.png]]
## 특징
1. **안정적인 IP 주소**: 서비스는 포드에 액세스할 수 있는 안정적인 단일 IP 주소와 DNS 이름을 제공합니다. 서비스를 지원하는 포드가 변경되더라도 이는 안정적으로 유지됩니다.
2. **로드 밸런싱**: 서비스는 네트워크 트래픽을 여러 포드에 분산하여 포드 간에 요청을 효과적으로 로드 밸런싱할 수 있습니다. 이는 애플리케이션의 고가용성과 내결함성을 보장하는 데 중요합니다.
3. **서비스 검색**: 서비스는 실행 중인 Pod 검색을 용이하게 하여 애플리케이션이 서로를 찾고 클러스터 내에서 쉽게 통신할 수 있도록 합니다.
4. **추상화**: 실제 백엔드 Pod IP를 추상화함으로써 서비스를 사용하면 Pod IP 주소를 추적할 필요 없이 서비스 뒤의 Pod를 변경할 수 있습니다.

## Service 유형
1. [[ClusterIP]]: 클러스터 내부의 다른 앱이 액세스할 수 있는 클러스터 내부 서비스를 제공. 서비스는 클러스터 내의 다른 Pod가 서비스의 Pod에 액세스하는 데 사용할 수 있는 IP 주소를 가져옵니다.
2. [[NodePort]]: 이 유형의 서비스는 정적 포트(NodePort)에서 각 노드의 IP에 서비스를 노출합니다. NodePort 서비스가 라우팅되는 ClusterIP 서비스가 자동으로 생성됩니다. `<NodeIP>:<NodePort>`를 사용하여 클러스터 외부에서 액세스할 수 있습니다.
3. [[LoadBalancer]]: 이 서비스 유형은 클라우드 공급자의 로드 밸런서를 사용하여 서비스를 외부에 노출합니다. 외부 로드 밸런서가 라우팅되는 NodePort 및 ClusterIP 서비스가 자동으로 생성됩니다.
4. [[ExternalName]]: 해당 값과 함께 CNAME 레코드를 반환하여 'externalName' 필드(예: 'foo.bar.example.com')의 콘텐츠에 서비스를 매핑합니다. 프록시가 설정되지 않았습니다.



#### 기본 yaml
```bash
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

#### loadbalancer 타입
```bash
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer  # Change this line to specify the service type as LoadBalancer
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
