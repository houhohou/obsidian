#kubernetes 
[[kubernetes]]에서 영구적인 볼륨에 관한 데이터 저장을 담당.
[[kubernetes API]]의 일부이며 스토리지가 소비되는 방식에서 세부정보를 관리하고 추상화 하는데 사용

## 특징
- **종속성**: [[namespace]]에 종속되지않음
- **독립적인 수명주기**: 포드에 대해 일시적인 볼륨과 달리 PV는 [[Pod]] 수명 주기를 넘어 존재합니다.
- **스토리지 추상화**: PV는 스토리지 리소스에 대한 추상화를 제공하여 스토리지 하드웨어가 [[Pod]]의 소비와 분리될 수 있도록 합니다.
- **용량 관리**: 관리자는 다양한 스토리지 용량, 성능 등을 갖춘 다양한 PV를 프로비저닝할 수 있습니다.


##### Access modes
접근 방식 관련 옵션 (스토리지에 따라 지원 옵션 다름)

**ReadWriteOnce (RWO)**
읽기/쓰기 접근을 허용하며, 동시에 하나의 파드만 해당 볼륨을 마운트하여 사용.
데이터베이스 서버에 사용
하나의 파드만 해당 데이터베이스에 읽기와 쓰기를 동시에 수행.

**ReadOnlyMany (ROX)**
읽기 전용 접근을 허용하며, 여러 파드가 볼륨을 마운트하여 데이터를 읽을 수 있지만 쓰기는 허용되지 않음
정적인 컨텐츠를 제공하는 웹 서버에 사용.
여러 파드가 동일한 데이터를 읽어올 수 있지만 수정은 하나의 파드만 수행

**ReadWriteMany (RWX)**
읽기/쓰기 접근을 허용하며, 여러 파드가 동시에 볼륨을 마운트하여 데이터를 읽고 쓸 수 있음
RWX 모드는 여러 파드가 동일한 데이터를 공유해야 하는 경우 유용
그러나 많은 스토리지 백엔드에서 지원되지 않을 수 있음

##### persistentVolumeReclaimPolicy
영구볼륨의 삭제 시 처리방식에 따라 Retain, Delete, Recycle 정책으로 나눔
Retain : pvc삭제시 데이터가 남아서 회수하거나 다른곳에 사용됨
Delete : pvc 삭제시 같이 사라져서 불필요한 스토리지 소비안됨
Recycle : pvc 삭제시 안에 데이터가 같이 삭제되지만 pv는 삭제 되지않아서 다른곳에 빨리 재활용되기 위해 사용
##### storageClassName
label같이 pv에 지정해두면 pvc에 storageClassName이 같은 녀석만 bound시켜준다. 


**📌 PV 리소스 주요 필드**  
- .spec.capacity.storage : 스토리지 용량 지정  
- .spec.accessMode : 접근 방식 관련 옵션 (스토리지에 따라 지원 옵션 다름)  
    - ReadWriteOnce : 하나의 파드만 읽기/쓰기 가능  
    - ReadWriteMany : 여러 파드 읽기/쓰기 가능  
    - ReadOnlyMany : 여러 파드 읽기만 가능  
- .spec.persistentVolumeReclaimPolicy : 회수 정책  
    - Retain  
    - Delete  
    - Recycle  
- .spec.nfs : NFS 볼륨  
    - 앞서 살펴본 emptyDir, hostPath와 같은 볼륨을 지정  
    - 볼륨에 따른 하위 필드는 달라짐

1. 물리적 볼륨
```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 20Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: manual #pvc를 설정할때 label같이 같아야 bound시켜준다
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
  name: test-pv  
spec:  
  capacity: #용량  
    storage: 1Gi # PersistentVolume(PV) 사이즈를 지정한다.  
  accessModes:  
    - ReadWriteMany #여러 클라이언트를 위한 읽기 쓰기 마운트  
  nfs:  
    server: 192.168.56.30 # nfs서버의 ip주소  
    path: /nfs #nfs서버에서 공유한 디렉토리명
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


**📌 PV 리소스의 상태(STATUS)**  
- Available : 다른 PVC에 연결되지 않은 상태, 사용 가능 상태  
- Bound : 특정 PVC에 연결됨  
- Released : 연결되었던 PVC가 해제되었으며, 리소스를 회수하지 않은 상태  
- Failed : 회수 실패