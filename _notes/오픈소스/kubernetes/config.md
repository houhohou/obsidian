	#kubernetes 


[[kubernetes]]에서 [[kubectl]] 이 명령내릴때 사용자 자격 증명을 결정하는 구성파일

현재 터미널 작업
``` bash
#현재 터미널에 config파일 확인
echo $KUBECONFIG
./.admin.conf

# 현재 터미널에 config파일 설정
export KUBECONFIG=./.admin.conf

# ./.admin.conf 파일을 config파일로 지정하고 명령시행
kubectl --kubeconfig ./.admin.conf
```

~
컨텍스트 확인 & 전환
```bash
#config 파일 변경
KUBECONFIG=~/.kube/config

# 현재 컨텍스트 표시
kubectl config currrent-context

# 컨텍스트 목록 전체 표시
kubectl config get-contexts

# 컨텍스트 전환
kubectl config use-context [context_name]

# 명령어 실행마다 컨텍스트 지정 가능
kubectl --context prd-admin get pod
```


❗변경방법
1. 직접 config변경
2. 명령어로 변경

직접 config변경
```bash
/etc/kubernetes/admin.conf
~/.kube/config
```

명령어로 변경
``` bash
# 클러스터 정의를 추가, 변경
kubectl config set-cluster prd-cluster \
--server=https://localhost:6443

# 인증 정보 정의를 추가, 변경
kubectl config set-credentials admin-user \
--client-certificate=./sample.crt \
--client-key=./sample.key \
--embed-certs=true

# 컨텍스트 정의(클러스터/인증 정보/네임스페이스 정의)를 추가, 변경
kubectl config set-context prd-admin \
--cluster=prd-cluster \
--user=admin-user \
--namespace=default
```

## 여러 kubernetes 통합 관리하게 config 파일 수정


kubeconfig 설정을 표시하는 기본 명령어 (병합된 것도 포함)
``` bash
#기본 명령어
kubectl config view

#환경변수에 지정된 모든 kubeconfig 파일을 병합
kubectl config view --merge

#외부파일에 대한 모든참조가 실제 kubeconfig파일로 대체되게 출력
kubectl config view --flatten
```


##### 병합해보기
```bash
#현재 config_company config_my_labtop 두가지 config가 있다. 
$ls
config_company   config_my_labtop

#두 파일을 kubectl config view로 병합
KUBECONFIG=~/.kube/config_company:~/.kube/config_my_labtop kubectl config view --merge --flatten > ~/.kube/merged-config

#파일 생성확인
$ls
config_company   config_my_labtop merged-config

#config 파일로 변경
mv ./merged-config ./config

#컨텍스트 상태에 두개의 cluster가 뜨는지 확인
$kubectl config get-contexts
CURRENT   NAME                             CLUSTER         AUTHINFO
*         kubernetes-admin@cluster.local   cluster.local   kubernetes-admin
          kubernetes-admin@kubernetes      kubernetes      kubernetes-admin
```

![](https://i.imgur.com/LQHgQMy.png)


많은 클러스터를 관리할때는 [[kubectx & kubens]]를 사용해보자

