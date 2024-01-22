#kubernetes 
[[kubernetes]]에서 사용자가 CLI를 통해 [[kubernetes API]]에 명령을 내릴수 있게 하는 command

[[config]] 파일이 해당 서버에 존재하고 이를 통해 kubectl이 [[kubernetes API]]에 명령을 내림

✅ **요소**
✔️ clusters : 접속대상 클러스터 정보
✔️ users : 인증정보
✔️ contexts : cluster, user, namespace를 지정한 것을 정의

👉 기본적인 위치
```bash
/etc/kubernetes/admin.conf
~/.kube/config
```

👉 구성
``` yaml
apiVersion: v1
kind: Config
preferences: {}
clusters: # 접속 대상 클러스터
  - name: sample-cluster
    cluster:
      server: https://localhost:6443
users: # 인증 정보
  - name: sample-user
    user:
      client-certificate-data: LS0tLS1CRUdJTi...
      client-key-data: LS0tLS1CRUdJTi...
contexts: # 접속 대상과 인증 정보 조합
  - name: sample-context
    context:
      cluster: sample-cluster
      namespace: default
      user: sample-user
current-context: sample-context
```
[[config]]

kubectl은 컨텍스트(current-context)를 전환해서 여러 환경을 여러 권한으로 조작가능