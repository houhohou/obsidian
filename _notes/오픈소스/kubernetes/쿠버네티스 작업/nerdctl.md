#kubernetes 
[[kubernetes]] 혹은 [[docker]]에서 컨테이너 런타임 사용할때모두 통용되는 명령어

- [b] 다운로드 
```bash
# 링크 복사 후 다운로드
wget https://github.com/containerd/nerdctl/releases/download/v1.4.0/nerdctl-full-1.4.0-linux-amd64.tar.gz

# /usr/local에 압축 해제
tar Cxzvvf /usr/local nerdctl-full-1.4.0-linux-amd64.tar.gz

# root 계정에서 사용하기 위해 .local에 링크 생성
mkdir -p ~/.local/bin && cd ~/.local/bin
ln -s /usr/local/bin/runc runc
ln -s /usr/local/bin/nerdctl nerdctl
ln -s /usr/local/bin/containerd containerd
ln -s /usr/local/bin/ctr ctr

# nerdctl 버전 확인
nerdctl -v
#$ nerdctl version 1.4.0
```


📙 명령어
```bash
# namespace 목록 확인
nerdctl namespace ls

# container 목록 확인
nerdctl container ls

```

📙 [[Harbor]]같은 priviate 레지스트리 로그인할때,
```
nerdctl --insecure-registry login 10.185.214.230:80 -u admin -p cloud1234
```