#kubernetes 
[[kubernetes]]에서 영구적인 볼륨에 관한 데이터 저장을 담당.
[[kubernetes API]]의 일부이며 스토리지가 소비되는 방식에서 세부정보를 관리하고 추상화 하는데 사용

## 특징
- **종속성**: [[namespace]]에 종속되지않음
- **독립적인 수명주기**: 포드에 대해 일시적인 볼륨과 달리 PV는 [[Pod]] 수명 주기를 넘어 존재합니다.
- **스토리지 추상화**: PV는 스토리지 리소스에 대한 추상화를 제공하여 스토리지 하드웨어가 [[Pod]]의 소비와 분리될 수 있도록 합니다.
- **용량 관리**: 관리자는 다양한 스토리지 용량, 성능 등을 갖춘 다양한 PV를 프로비저닝할 수 있습니다.



볼륨 종류에 따라

1. 물리적 볼륨
```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 100Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage
  local:
    path: /mnt/disks/ssd1
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - example-node
```

2. NFS
```yaml
---

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-name
spec:
  capacity:
    storage: 5Gi #스토리지 크기
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.0
  nfs:
    path: /dir/path/on/nfs/server
    server: nfs-server-ip-address
---
```


4. 클라우드


```yaml
	#구글클라우드 스토리지 백엔드예시
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: test-volume
  labels:
    failure-domain.beta.kubernetes.io/zone: us-central1-a__us-central1-b
spec:
  capacity:
    storage: 400Gi #스토리지 크기
  accessModes:
  - ReadWriteOnce
  gcePersistentDisk:
    pdName: my-data-disk #parameters
    fsType: ext4
```