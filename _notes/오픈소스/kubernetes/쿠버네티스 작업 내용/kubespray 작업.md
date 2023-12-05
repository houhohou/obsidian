#kubernetes 
[[kubernetes]] 스프레이로 간단하게 작업하는 방법
Ubuntu 22.04

## 환경
모두 인터넷 되는환경!!

kubespray-2.22

ubuntu 22.04
IP
192.168.34.45 deploy
192.168.34.46 master01
192.168.34.47 node01
192.168.34.49 node02


호스트 설정
```/bin/bash
#all
cat >> /etc/hosts << E0F
192.168.34.45 deploy
192.168.34.46 master01
192.168.34.47 node01
192.168.34.49 node02
E0F
```

ssh 키 복사
```/bin/bash
#master
ssh-keygen -t rsa
ssh-copy-id root@192.168.34.46
ssh-copy-id root@192.168.34.47
ssh-copy-id root@192.168.34.49
```

kubespray 클론
```/bin/bash
#deploy
sudo apt install python3-pip
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray/
git checkout release-2.22
```


python3 가상환경 접속
```/bin/bash
python3 -m venv kubespray-venv
. kubespray-venv/bin/activate
```



버전 세팅
```/bin/bash
#deploy
sudo pip3 install -r requirements.txt 
cp -r inventory/sample inventory/cluster
declare -a IPS=(192.168.34.47 192.168.34.49)
CONFIG_FILE=inventory/cluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

![](https://i.imgur.com/LlukOIG.png)

#오류날때

![](https://i.imgur.com/xnDsPM5.png)
아래처럼 설치!
```/bin/bash
pip3 install ruamel_yaml
```


아래처럼 작업
![](https://i.imgur.com/U525gkd.png)

addon 수정
```/bin/bash
vi inventory/cluster/group_vars/k8s_cluster/addons.yml
vi inventory/cluster/group_vars/k8s_cluster/k8s-cluster.yml
```

시작
```/bin/bash
ansible-playbook -i inventory/cluster/hosts.yaml --become --become-user=root --extra-vars ansible_sudo_pass=cloud1234 cluster.yml

```


```/bin/bash

```

여기보고 따라함
https://jeongchul.tistory.com/710

