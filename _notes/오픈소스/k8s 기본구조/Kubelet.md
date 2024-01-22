#kubernetes 
✅ [[kubernetes]]의 [[워커노드]]에서 실행되어 API와 통신해서 [[Pod]]가 정상 작동 되도록 도움
Scheduler가 결정해 API가 그것을 받아 적절한 [[워커노드]]에 위치한 kubelet에게 명령내림

✅ 노드의 상태를 관리하고 마스터와의 통신을 담당

K8S 1.17이상에서 API서버는 하트비트를 통해 kubelet이 정상인 것을 계속 인식
❗하트비트 : 실행중인 클러스터의 kube-node-lease네임스페이스를 살펴보는것으로 유지


👉CNI : 컨테이너 네트워크 인터페이스

👉CRI : 컨테이너 런타임 인터페이스
			반드시 컨테이너를 시작할 수 있는 컨테이너 엔진이 필요. 
			ex) Docker engine, CRI-O, LXC
			
```bash
# Kubelet 서비스 확인
systemctl status kubelet

🟢 kubelet.service - Kubernetes Kubelet Server
     Loaded: loaded (/etc/systemd/system/kubelet.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2023-11-18 05:13:00 UTC; 1 week 6 days ago
       Docs: https://github.com/GoogleCloudPlatform/kubernetes
   Main PID: 13397 (kubelet)
      Tasks: 22 (limit: 19050)
     Memory: 56.5M
        CPU: 7h 9min 1.561s
     CGroup: /system.slice/kubelet.service
     
```


## 주요기능
👉 **파드 관리**
Kubelet은 마스터로부터 할당된 파드를 노드에 배치하고 실행. 파드의 상태를 주기적으로 모니터링하고, 만약 문제가 발생하면 파드를 다시 시작하거나 재스케줄링하여 안정성을 유지.
👉 **컨테이너 실행**
Kubelet은 파드에 정의된 컨테이너를 실행하고 관리. 컨테이너 런타임(Docker, containerd 등)을 사용하여 컨테이너를 시작하고 중지하며, 파드에 정의된 리소스 요구 사항에 따라 리소스를 할당
👉 **리소스 모니터링**
Kubelet은 노드의 리소스 사용량을 모니터링하여 클러스터의 상태를 파악. 이 정보는 마스터에 보고되어 클러스터의 스케줄링 결정에 사용.
👉 **상태 보고**
Kubelet은 주기적으로 노드와 파드의 상태를 마스터에 보고합니다. 이를 통해 마스터는 클러스터의 전체 상태를 파악하고 각 노드와 파드의 상태를 추적할 수 있습니다.
👉 **노드의 자동 복구**
Kubelet은 노드가 비정상적으로 종료되었을 때 자동으로 복구를 시도합니다. 이를 통해 노드의 가용성을 유지하고, 노드 장애 시 클러스터의 안정성을 보장합니다.

## 구성요소
**Kubeconfig**
Kubeconfig는 Kubelet이 마스터와 통신하는 데 사용되는 구성 파일입니다. 클러스터의 API 서버 주소, 인증 정보 등이 포함되어 있습니다.
**CAdvisor**
CAdvisor는 Kubelet의 하위 프로세스로 컨테이너의 리소스 사용량과 성능 지표를 수집합니다. 이 정보는 Kubelet에 보고되고, 클러스터의 상태 모니터링에 사용됩니다.
**Kubelet API Server**
Kubelet은 내부 API 서버를 제공하여 kubelet에 대한 요청을 처리합니다. 이를 통해 kubelet은 자체적으로 파드 정보를 조회하고 관리할 수 있습니다.
**OOM (Out-Of-Memory) Monitor**
OOM Monitor는 Kubelet이 파드의 OOM 이벤트(메모리 부족)를 처리하는데 사용됩니다. OOM 이벤트 발생 시, 해당 파드를 중지하거나 다시 시작하여 다른 파드의 안정성을 보장합니다.

## 동작방식
1. 클러스터 마스터에서 파드를 할당받음
2. CAdvisor를 통해 컨테이너의 리소스 사용량과 성능 정보 수집
3. Kubelet API Server를 통해 파드의 정보 조회와 갱신
4. 파드에 정의된 컨테이너 실행과 관리
5. 주기적으로 마스터에 노드와 파드의 상태 보고

## 보안
Kubelet은 클러스터의 핵심 구성 요소로, 보안에 특히 신경써야 합니다. kubeconfig 파일은 안전하게 관리되어야 하며, 액세스 권한을 최소화하여 클러스터에 접근하는 경우를 제한해야 합니다. 또한, 적절한 네트워크 정책과 노드 보안 그룹 등을 구성하여 외부에서의 액세스를 제어해야 합니다.













