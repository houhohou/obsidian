#kubernetes 
[[kubernetes]]에서 [[LoadBalancer]]를 담당




##### 설치
```bash
# namespace와 metallb 설치
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.5/config/manifests/metallb-native.yaml


# IP Address Pool 생성
cat >> pool.yaml << E0F
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: metallb-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.0.240-192.168.0.250
E0F

# 실행
kubectl apply -f pool.yaml

# L2Advertisement 생성
cat >> l2.yaml << E0F
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: metallb-l2
  namespace: metallb-system
E0F

# 실행
kubectl apply -f l2.yaml
```




