#kubernetes

[[kubernetes]] 를 가상적인 클러스터 분리 기능을 사용할때 쓰임. 

용도 : 하나의 쿠버네티스를 여러 개발팀이 사용할때

❗완벽한 분리 기능은 아님!

✅ **기본 설정 namespace** 는 총 4가지가 있다

👉 kube-system 
- 클러스터 구성요소, 애드온이 배포
👉 kube-public
- 모든 사용자가 공통으로 사용하는 설정값
👉 kube-node-lease
- 노드 하트비트정보 저장
👉 default
- 기본 네임스페이스