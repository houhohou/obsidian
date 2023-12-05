[[kubespary배포전 할일들]]
Kubespray version 2.22.0

!!! 모든건 deployment 서버에서 진행 !!!



가상환경 생성기 설치
```/bin/bash
apt install python3.8-venv -y
```


가상환경 생성 및 접속
```/bin/bash
python3 -m venv .venv
source .venv/bin/activate
```

호환버전 설치위해 python3.8버전으로 설치
```/bin/bash
apt -y install git python3.8 python3-pip && python3.8 -m pip install pip
```

github에서 kubespray 오픈소스 프로젝트 복제
```/bin/bash
git clone --single-branch --branch=v2.22.0 https://github.com/kubernetes-sigs/kubespray.git
```

설정파일 백업
```/bin/bash
cp -rpf kubespray/inventory/sample/ kubespray/inventory/mycluster 
cp -p kubespray/roles/download/defaults/main.yml{,.bak} 
cp -p kubespray/inventory/mycluster/group_vars/all/all.yml{,.bak} 
cp -p kubespray/inventory/mycluster/group_vars/all/containerd.yml{,.bak} 
cp -p kubespray/inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml{,.bak} 
cp -p kubespray/inventory/mycluster/group_vars/k8s_cluster/k8s-net-cilium.yml{,.bak} 
cp -p kubespray/inventory/mycluster/inventory.ini{,.bak}
```

작업파일 이동
```/bin/bash
cd kubespray/
```
ansible 실행하기 위한 패키지 설치
```/bin/bash
pip install wheel
pip3 install -r requirements.txt
```

vip도 도메인 등록
```/bin/bash
cat << EOF >> /etc/hosts
10.185.214.200 vip.haproxy
EOF
```


haproxy 설정
```/bin/bash
---
- hosts: haproxy
  become: yes
  tasks:
  - name: proxy liten setting for vip
    shell: |
      cat >> /etc/haproxy/haproxy.cfg << E0F

      listen k8s_cluster
              bind 10.185.214.200:6443
              balance source
              option tcpka
              option tcplog
              mode tcp
              server master1 10.185.214.221:6443 check inter 2000 rise 2 fall 5
              server master2 10.185.214.222:6443 check inter 2000 rise 2 fall 5
              server master3 10.185.214.223:6443 check inter 2000 rise 2 fall 5
      E0F

```
방화벽 허용
```/bin/bash
a haproxy -m command -a "ufw allow 6443/tcp"
```

pcs 상태 확인
```/bin/bash
a haproxy -m command -a "pcs status"
```

![](https://i.imgur.com/EwGWXl7.png)


haproxy 리셋
```/bin/bash
a haproxy -m command -a "systemctl restart haproxy"
```
!! haproxy02는 에러뜨는게 당연!(vip가 현재는 haproxy01에 있으므로)
![](https://i.imgur.com/nmCUDam.png)


haproxy 상태 확인
```/bin/bash
a haproxy -m command -a "systemctl is-active haproxy"
```
!! haproxy02는 failed뜨는게 맞다!!

![](https://i.imgur.com/DUkLb3q.png)

all.yml파일 설정
```/bin/bash
sed -i '4ipackage_ip: 10.185.214.210:10880\nimage_repo: 10.185.214.210:60005' inventory/mycluster/group_vars/all/all.yml
sed -i '20iapiserver_loadbalancer_domain_name: "vip.haproxy"\nloadbalancer_apiserver:\n  address: 10.185.214.200\n  port: 6443' inventory/mycluster/group_vars/all/all.yml

```
containerd 설정파일 편집
```/bin/bash
sed -i '41i\containerd_insecure_registries:\n#   "localhost": "<http://127.0.0.1>"\n  "10.185.214.210:60005": "http://10.185.214.210:60005"' inventory/mycluster/group_vars/all/containerd.yml
echo -e "containerd_registry_auth:\n  - registry: 10.185.214.210:60005\n    username: admin\n    password: It1" >> inventory/mycluster/group_vars/all/containerd.yml
```

main.yml 설정 파일 편집
```/bin/bash
sed -i 's/"gcr.io"/"{{ image_repo }}"/g' roles/download/defaults/main.yml
sed -i 's/registry\.k8s\.io/{{ image_repo }}/g' roles/download/defaults/main.yml
sed -i 's/docker\.io/{{ image_repo }}/g' roles/download/defaults/main.yml
sed -i 's/quay\.io/{{ image_repo }}/g' roles/download/defaults/main.yml
sed -i 's/ghcr\.io/{{ image_repo }}/g' roles/download/defaults/main.yml
sed -i '159,182s/^/#/' roles/download/defaults/main.yml
```
main.yml 설정 파일 편집 2
```/bin/bash
cat >> kubesprayconf.txt << E0F
kubelet_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/kubelet" kubectl_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/kubectl" kubeadm_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/kubeadm" etcd_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/etcd-{{ etcd_version }}-linux-{{ image_arch }}.tar.gz" cni_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/cni-plugins-linux-{{ image_arch }}-{{ cni_version }}.tgz" ciliumcli_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/cilium-linux-{{ image_arch }}.tar.gz" crictl_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/crictl-{{ crictl_version }}-{{ ansible_system | lower }}-{{ image_arch }}.tar.gz" runc_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/runc.{{ image_arch }}" containerd_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/containerd-{{ containerd_version }}-linux-{{ image_arch }}.tar.gz" nerdctl_download_url: "http://{{ package_ip }}/kube-{{ kube_version }}/nerdctl-{{ nerdctl_version }}-{{ ansible_system | lower }}-{{ image_arch }}.tar.gz"
E0F
sed -i "183r kubesprayconf.txt" roles/download/defaults/main.yml
rm -rf kubesprayconf.txt


```
k8s-cluster.yml 설정 파일 편집

k8s버전수정
network plugin을 cilium으로 변경
network plugin multus를 true로 설정(여러 네트워크 인터페이스 연결가능)
nodelocaldns비활성화
container manager containerd로 변경
```/bin/bash
sed -i 's/^kube_version:.*$/kube_version: v1.26.3/' inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

sed -i 's/^kube_network_plugin:.*$/kube_network_plugin: cilium/' inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

sed -i 's/^kube_network_plugin_multus:.*$/kube_network_plugin_multus: true/' inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

sed -i 's/^enable_nodelocaldns:.*$/enable_nodelocaldns: false/' inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

sed -i 's/^container_manager:.*$/container_manager: containerd/' inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

```
k8s-net-cilium.yml 설정편집
 - hubble 설치 진행하지않고 활성화하는 설정
```/bin/bash
cat >> sprayset.txt << E0F
cilium_enable_hubble: true
cilium_enable_hubble_metrics: true
cilium_hubble_metrics:
- dns
- drop
- tcp
- flow
- icmp
- http
E0F
sed -i '158 r sprayset.txt' inventory/mycluster/group_vars/k8s_cluster/k8s-net-cilium.yml

rm -rf sprayset.txt
```



inventory.ini 설정 편집
```/bin/bash
sed -i '/\[etcd\]/a master01\nmaster02\nmaster03' inventory/mycluster/inventory.ini
sed -i '/\[kube_control_plane\]/a master01\nmaster02\nmaster03' inventory/mycluster/inventory.ini
sed -i '/\[kube_node\]/a mgmt01\nmgmt02\nmgmt03\nnode01\nnode02\nnode03\ningress01\ningress02\nstorage01\nstorage02\nstorage03' inventory/mycluster/inventory.ini

sed -i '/\[all\]/a master01 ansible_host=10.185.214.221 ip=10.185.214.221\nmaster02 ansible_host=10.185.214.222 ip=10.185.214.222\nmaster03 ansible_host=10.185.214.223 ip=10.185.214.223\nnode01 ansible_host=10.185.214.226 ip=10.185.214.226\nnode02 ansible_host=10.185.214.227 ip=10.185.214.227\nnode03 ansible_host=10.185.214.228 ip=10.185.214.228\nmgmt01 ansible_host=10.185.214.231 ip=10.185.214.231\nmgmt02 ansible_host=10.185.214.232 ip=10.185.214.232\nmgmt03 ansible_host=10.185.214.233 ip=10.185.214.233\ningress01 ansible_host=10.185.214.213 ip=10.185.214.213\ningress02 ansible_host=10.185.214.214 ip=10.185.214.214\nstorage01 ansible_host=10.185.214.236 ip=10.185.214.236\nstorage02 ansible_host=10.185.214.237 ip=10.185.214.237\nstorage03 ansible_host=10.185.214.238 ip=10.185.214.238' inventory/mycluster/inventory.ini
```

anonymous access 허용

![](https://i.imgur.com/CoXjL8w.png)



nexus image repo 포트 오픈
```/bin/bash
ufw allow 60005/tcp
```

아래에서 실행되어져야함!!
![](https://i.imgur.com/RysSizz.png)


Ansible로 k8s 클러스터 배포전 통신확인
```/bin/bash
ansible all -m ping -i inventory/mycluster/inventory.ini
```
------ 이거만 하면 됨 -------

Ansible로 배포
```/bin/bash
ansible-playbook -i inventory/mycluster/inventory.ini --become --become-user=root cluster.yml
```


배포 완료 후 kubeadm_certificate_key.creds 파일 생성확인
```/bin/bash
cat inventory/mycluster/credentials/kubeadm_certificate_key.creds
```

kubectl 명령어 설치(master서버에만 설치되어있어서 deployment서버에서도 진행)
```/bin/bash
exit
ssh root@deploy
cd /root
curl -LO https://dl.k8s.io/release/v1.26.3/bin/linux/amd64/kubectl
```

설치전 깔끔히 제거
```/bin/bash
install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl rm kubectl
```

.kube디렉터리 생성, apiserver와 통신하기 위해 admin.conf 파일 복사
```/bin/bash
mkdir .kube
scp root@10.185.214.221:/etc/kubernetes/admin.conf /root/.kube/config
```

마지막 확인
```/bin/bash
kubectl get nodes -o wide
```
```/bin/bash

```


참고 블로그 ( python venv )
https://ksr930.tistory.com/306



