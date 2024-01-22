#오픈스택
[[오픈스택]]  컴포넌트인 [[cinder]] 에 [[ceph]]를  스토리지로 사용

| Description | name | Disk | size | OS | IP | Public IP | 
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | 
| ceph control server | cephadmin | - | - | ubuntu20.04 | 10.185.214.65 | 192.168.34.55 | 
| openstack controller1 | con01 | - | - | ubuntu20.04 | 10.185.214.61 | - | 
| openstack controller2 | con02 | - | - | ubuntu20.04 | 10.185.214.62 | - | 
| openstack controller3 | con03 | - | - | ubuntu20.04 | 10.185.214.63 | - |
| node 1 | ceph01 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.66 | 192.168.34.67 | 
| node 2 | ceph02 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.67 | 192.168.34.56 | 
| node 3 | ceph03 | /dev/sdb | 100G | ubuntu20.04 | 10.185.214.68 | 192.168.34.36 | 


- [b] ceph admin 설정
```bash
#  cephadmin 서버에서 실행할거 
# ceph클러스터에 'volumes'라는 새 풀을 생성 '128'은 pg 
ceph osd pool create volumes 128
# 생성됐는지 확인
ceph osd pool ls
---
device_health_metrics
volumes
---
# pool init
rbd pool init volumes

# ceph client.nova의 인증정보 및 키링파일 생성
ceph auth get-or-create client.cinder mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=volumes, allow rx pool=images' -o /etc/ceph/ceph.client.volumes.keyring

# 인증정보 확인
ceph auth get client.cinder
---
[client.cinder]
	key = AQC4JqplwDsbChAAi8c+9yNe6q8g2ezOW2udQg==
	caps mon = "allow r"
	caps osd = "allow class-read object_prefix rbd_children, allow rwx pool=volumes, allow rx pool=images"
exported keyring for client.cinder
---


# 생성 된 키링 파일을 nova compute 서비스 실행중인 compute node로 복사
cat /etc/ceph/ceph.client.volumes.keyring
scp /etc/ceph/ceph.client.volumes.keyring root@con01:/etc/ceph/
scp /etc/ceph/ceph.client.volumes.keyring root@con02:/etc/ceph/
scp /etc/ceph/ceph.client.volumes.keyring root@con03:/etc/ceph/


```
- [b] controller server
```bash
# con01 con02 con03 권한 변경
chown cinder. /etc/ceph/ceph.client.volumes.keyring
chmod 640 /etc/ceph/ceph.client.volumes.keyring

# con01 con02 con03 conf 복사 
scp root@cephadmin:/etc/ceph/ceph.conf /etc/ceph/

cat >> /etc/ceph/ceph.conf << E0F
[client.cinder]
keyring = /etc/ceph/ceph.client.volumes.keyring
E0F

# con01 con02 con03 서버에서 따로 uuid 때려넣어준다. 
uuidgen
---
f8d4c090-7757-4c48-a3f4-3b25644ff20f
---

# vi /etc/cinder/cinder.conf & 각 서버의 uuid값 각각 넣어줘야함
[DEFAULT]
enabled_backends = lvm,ceph
glance_api_version = 2

[ceph]
volume_driver = cinder.volume.drivers.rbd.RBDDriver
rbd_cluster_name = ceph
rbd_pool = volumes
rbd_user = cinder
rbd_ceph_conf = /etc/ceph/ceph.conf
rbd_flatten_volume_from_snapshot = false
rbd_secret_uuid = f8d4c090-7757-4c48-a3f4-3b25644ff20f
rbd_max_clone_depth = 5
rbd_store_chunk_size = 4
rados_connect_timeout = -1

# cinder  재시작
systemctl restart openstack-cindre-volume
```
- [b] 테스트
```bash
# openstack controller 서버
# 볼륨 생성
openstack volume create --size 7 ceph-volume01

# 생성확인 ( 사이즈 7 생성된거 확인)
openstack volume list 
| ID                                   | Name          | Status    | Size | Attached to                                                   |
+--------------------------------------+---------------+-----------+------
| db001ab6-6058-4b8e-a9bd-e44e8c06c2de | ceph-volume01 | available |    7 |                                                              |

# cephadmin 서버
# 볼륨 id 값 확인
rbd -p volumes ls
volume-db001ab6-6058-4b8e-a9bd-e44e8c06c2de

# id 값으로 상세 확인 ( size 7 확인 )
rbd -p volumes info volume-db001ab6-6058-4b8e-a9bd-e44e8c06c2de
rbd image 'volume-db001ab6-6058-4b8e-a9bd-e44e8c06c2de':
	size 7 GiB in 1792 objects
	order 22 (4 MiB objects)
	snapshot_count: 0
	id: 38d95a4705e3
	block_name_prefix: rbd_data.38d95a4705e3
	format: 2
	features: layering, exclusive-lock, object-map, fast-diff, deep-flatten
	op_features:
	flags:
	create_timestamp: Fri Jan 19 17:59:32 2024
	access_timestamp: Fri Jan 19 17:59:32 2024
	modify_timestamp: Fri Jan 19 17:59:32 2024
```



- [!] 참고블로그
 https://techpicnic.tistory.com/254
