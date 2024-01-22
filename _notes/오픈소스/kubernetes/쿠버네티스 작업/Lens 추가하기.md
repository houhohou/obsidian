#kubernetes 
[[kubernetes]] 대시보드 
환경

mac apple cpu
쿠버네티스 : 가상머신 1,2,3


openlens 설치
brew install --cask openlens


설치후
![](https://i.imgur.com/8BcKQJ6.png)


Browse Clusters in Catalog 누르면
![](https://i.imgur.com/BveUcMT.png)

클릭후 가상머신에서 
vi /etc/kubernetes/admin.conf 
파일에 복사해서 붙여넣는다
아래내용만 수정해서 
---
clusters:
- cluster:
    server: https://<VM_IP_ADDRESS>:6443
---

출처
https://hackerpark.tistory.com/entry/Kubernetes-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EB%8C%80%EC%8B%9C%EB%B3%B4%EB%93%9C-OpenLens-%EC%84%A4%EC%B9%98-k8s-dashboard-openlens-install


