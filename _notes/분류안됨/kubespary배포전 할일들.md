[[ubuntu 레포지터리]]

하기전에 할일
[[Ansible 설치]]


playbook으로 모두 설정할거니 모두 아래처럼 실행하면 된다. 

```/bin/bash
vi pb.yml
ansible-playbook pb.yml
```

swapoff 비활성화

```/bin/bash
---
- name: Disable Swap and Comment Out swap in /etc/fstab
  hosts: k8s
  become: yes
  tasks:
    - name: Turn off swap
      command: swapoff -a
      ignore_errors: yes
---
- name: Comment out swap in /etc/fstab
  hosts: k8s
  become: yes
  tasks:
    - name: Comment out swap entry in /etc/fstab using sed
      shell: sed -i -e '/swap/ s/^/#/' /etc/fstab
```

공백뜨면 ok
```/bin/bash
a k8s -m command -a "swapon -s"
```
![](https://i.imgur.com/dUs7KT5.png)


k8s 클러스터 구성하기 위해 overlay와 br_netfilter 모듈 추가 & 커널에 적재
```/bin/bash
---
- hosts: k8s_group
  become: yes
  tasks:
  - name: Add overlay and br_netfilter modules using tee 1
    shell: |
      cat << EOF | sudo tee /etc/modules-load.d/k8s.conf
      overlay
      br_netfilter
      EOF
      
  - name: Load overlay module
    command: modprobe overlay
  - name: Load br_netfilter module
    command: modprobe br_netfilter
```
커널 매개변수에 네트워크 브리지 관련 기능 및 IPv4 포워딩 활성화
```/bin/bash
---
- hosts: k8s
  become: yes
  tasks:
  - name: Add overlay and br_netfilter modules using tee 2
    shell: |
      cat << EOF | sudo tee /etc/sysctl.d/k8s.conf
      net.bridge.bridge-nf-call-iptables  = 1
      net.bridge.bridge-nf-call-ip6tables = 1
      net.ipv4.ip_forward                 = 1
      EOF
```
추가한 커널 매개변수에 추가
```/bin/bash
a k8s -m command -a "sysctl --system"
```
모듈이 로드되었는지 확인
```/bin/bash
a k8s -m command -a "lsmod" | grep overlay
a k8s -m command -a "lsmod" | grep br_netfilter
```
![](https://i.imgur.com/YNYHPc9.png)


추가한 커널 매개변수가 반영되었는지 확인
```/bin/bash
a k8s -m command -a "sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward"
```
![](https://i.imgur.com/aYJnmsH.png)



hosts추가
```/bin/bash
---
- hosts: k8s
  become: yes
  tasks:
  - name: Add /etc/hosts file in k8s group
    shell: |
      cat >> /etc/hosts << E0F
      10.185.214.200 vip.haproxy
      10.185.214.213 ingress01
      10.185.214.214 ingress02

      10.185.214.231 mgmt01
      10.185.214.232 mgmt02
      10.185.214.233 mgmt03

      10.185.214.236 storage01
      10.185.214.237 storage02
      10.185.214.238 storage03
      E0F
```

```/bin/bash

```