[[리눅스]] 가상화 기술인 [[virsh]] 네트워크 명령어
##### 네트워크 명령어
``` bash
#네트워크 확인
virsh net-list --all

#dhcp 
virsh net-dhcp-leases [네트워크 이름]
```

##### 네트워크 생성
'network_name.xml' 형식의 가상 네트워크 설정 파일을 만들어야 한다.

```bash

#네트워크 xml 생성
vi example_network.xml

<network>
    <name>internal1</name>
    <bridge name='virbr1' />
</network>

#네트워크 생성
virsh net-define example_network.xml

#네트워크 자동 시작
virsh net-autostart internal1

#네트워크 시작
virsh net-start internal1
```


##### 인스턴스에 네트워크 추가
```bash
#추가할 인스턴스 종료
virsh shutdown [인스턴스 이름]

#인스턴스 설정
virsh edit [인스턴스 이름]

#기존 네트워크 인터페이스 찾아서 다음과 같이 추가 후 저장 & mac address와 addresstype 자동생성확인
<interface type='network'>
    <source network='internal1' />
    <model type='virtio' />
</interface>

#가상 시스템 속성을 변경했으면 이를 적용하기 위해 libvirt-bin 서비스 재시작
service libvirt-bin restart

#네트워크 인터페이스 eth1 IP 주소정보를 insterfaces파일에 입력
vi /etc/network/interfaces

auto eth1
iface eth1 inet static
address 10.0.0.1
network 10.0.0.0
netmask 255.255.255.0
broadcast 10.0.0.255

#변경 저장후 eth1 활성화
ifup eth1

#네트워크 정보 확인
ifconfig

```




