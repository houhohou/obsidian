#kubernetes 
[[kubernetes]]

##버전목록
	기록날짜 : 23.09.16 ( 토 )
	OS : ubuntu 20.04

준비사항 
	🖥️ 테스트용 장비 스펙
		- memory : 2G
		- CPU : 2 Core
과정
1. 방화벽 off
1. swap off
2. Docker, Containerd 설치 ( 모든 master,worker node )
3. kubernetes 설치
	1. 
블로그 참조

https://domdom.tistory.com/591

cilium 설치( 잘된거임 )
https://blog.kubesimplify.com/how-to-install-a-kubernetes-cluster-with-kubeadm-containerd-and-cilium-a-hands-on-guide

cilium공식 깃
https://github.com/cilium/cilium-cli/releases/tag/v0.15.8

내 pc 토큰
kubeadm join 192.168.176.11:6443 --token 9vewzt.klxqloofu47ts8s9 \
	--discovery-token-ca-cert-hash sha256:97a23088fcf7d6c6369c15471157653b1cd2799b32515fe2fd9b1dd538e08429
	
##---------시작------------##
mkdir -p $HOME/.kube 
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config 
sudo chown $(id -u):$(id -g) $HOME/.kube/config


##--------방화벽 off
sudo ufw disable


##--------허용해야할 포트 

##--Master
sudo ufw enable 
sudo ufw allow 6443/tcp 
sudo ufw allow 2379:2380/tcp 
sudo ufw allow 10250/tcp 
sudo ufw allow 10251/tcp 
sudo ufw allow 10252/tcp 
sudo ufw status

##--Worker
sudo ufw enable 
sudo ufw allow 10250/tcp 
sudo ufw allow 30000:32767/tcp 
sudo ufw status


##----------swap off
sudo swapoff -a && sudo sed -i '/swap/s/^/#/' /etc/fstab


##----------docker, containerd 설치

##필수패키지 설치
sudo apt-get update 
sudo apt-get install -y \ 
ca-certificates \ curl \ gnupg \ lsb-release



sudo apt-get update 
sudo apt-get install -y apt-transport-https ca-certificates curl 

sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg 

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list