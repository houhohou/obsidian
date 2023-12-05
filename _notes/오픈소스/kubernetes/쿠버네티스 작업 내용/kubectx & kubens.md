#kubernetes 

[[kubernetes]] 에서 [[namespace]]와 [[config]]를 관리할때 유용한 오픈소스 

1. [[#config파일 변경하기|config파일 변경하기]]
2. [[#kubectx|kubectx]]
3. [[#kubens|kubens]]



🟢 다운로드 방법
```bash
brew install kubectx
```


사용법은 아래와 같다. 
```bash
# 등록된 클러스터 확인
$kubectx
kubernetes-admin@cluster.local
kubernetes-admin@kubernetes

# 네임스페이스 확인
$kubens
default
ingress-nginx
kube-node-lease
kube-public
kube-system
logging
monitor
```

## config파일 변경하기
👉이름이 길어서 줄이고싶다! 하면 아래를 따르자

현재 config에는 두개의 k8s가 묶여있다
```bash
# 현재 config에는 두개의 k8s가 묶여있다
$kubectx
kubernetes-admin@cluster.local
kubernetes-admin@kubernetes

# 백업
cp ~/.kube/config{,.bak}
```

👉vi 편집기로 부분 변경
```bash
vi config

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: 
#--------------토큰값----------------#
    server: https://192.168.34.46:6443            # 서버 A 경로
  name: company                                   # <----이부분 
- cluster:
    certificate-authority-data: 
#--------------토큰값----------------#
    server: https://192.168.176.11:6443            # 서버 B 경로
  name: localvm                                    # <----이부분
contexts:
- context:
    cluster: company                               # <----이부분
    user: sangm                                    # <----이부분
  name: sangm@company                              # <----이부분
- context:
    cluster: localvm                               # <----이부분
    user: sangm                                    # <----이부분
  name: sangm@localvm                              # <----이부분
current-context: sangm@company                     # <----이부분
kind: Config
preferences: {}
users:
- name: sangm                                       # <----이부분
  user:
    client-certificate-data:
#--------------토큰값----------------#
```


## kubectx
```bash
# 컨텍스트 전환
kubectx kubernetes-admin@cluster.local

# 바로 전 컨텍스트로 전환
kubectx -

# 컨텍스트 삭제
kubectx -d kubernetes-admin@cluster.local
```

## kubens

```bash
# 네임스페이스 전환
kubens default
```







