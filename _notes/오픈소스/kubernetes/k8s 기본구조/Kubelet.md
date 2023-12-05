#kubernetes 
[[kubernetes]]
모든 [[워커노드]]에서 실행되어 API와 통신해서 [[Pod]]가 정상 작동 되도록 도움
Scheduler가 결정해 API가 그것을 받아 적절한 [[워커노드]]에 위치한 kubelet에게 명령내림

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

