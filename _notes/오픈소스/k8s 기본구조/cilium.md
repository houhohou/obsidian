#kubernetes 

[[kubernetes]] & [[Doker]] 같은 [[리눅스]]에서 컨테이너용 네트워크 연결을 보호하기위해 [[eBPF]] 라는 기술을 사용하는 오픈소스 


**허블** : 완전히 분산된 네트워킹 및 보안 관찰 플랫폼. eBPF & cilium 기반으로 구축


kubernetes에서 네트워킹의 종류
1. 컨테이너 - 컨테이너 통신
2. [[Pod]] - [[Pod]] 통신
3. [[Pod]] - [[SVC]] 통신
4. External 통신 / [[ingress]] & [[Egress]]














# 명령어
```bash
# 설치
cilium install 

# 상태 확인
cilium status

# 제거
cilium uninstall 
```

https://blog.kubesimplify.com/how-to-install-a-kubernetes-cluster-with-kubeadm-containerd-and-cilium-a-hands-on-guide