#kubernetes 
[[kubernetes]]에서 다양한 스토리지에 대한 정책( 성능, 복제수준, 백업 정책) 을 사용해 스토리지 정의하고 제공


## 주요 기능
1. **동적 프로비저닝**: 아마도 StorageClasses의 가장 중요한 기능은 필요에 따라 영구 볼륨(PV)을 동적으로 프로비저닝하는 기능일 것입니다. 즉, 사용자가 [[PVC]](영구 볼륨 할당)를 생성하고 StorageClass를 지정하면 StorageClass에 정의된 사양에 따라 새 [[PV]]가 자동으로 생성되어 해당 PVC에 바인딩됩니다.
2. **스토리지 유형 정의**: StorageClass를 사용하면 관리자는 기본 스토리지 인프라를 기반으로 스토리지 유형을 정의할 수 있습니다. 예를 들어 클러스터에는 각각 서로 다른 특성을 갖는 "fast-ssd" 및 "standard-hdd"라는 StorageClass가 있을 수 있습니다.
3. **사용자 정의 및 매개변수**: 관리자는 디스크 성능, 복제 설정 또는 기본 스토리지 시스템이 지원하는 기타 스토리지별 구성을 포함할 수 있는 사용자 정의 매개변수를 사용하여 StorageClass를 정의할 수 있습니다.

## 예시
- **자동 볼륨 프로비저닝**: PVC가 특정 StorageClass를 요청하면 Kubernetes는 해당 클래스를 사용하여 필요한 PV를 동적으로 프로비저닝합니다.
- **서비스 품질**: 여러 StorageClass(예: 골드, 실버, 브론즈)를 정의하여 사용자는 애플리케이션 요구 사항에 따라 다양한 수준의 스토리지를 선택할 수 있습니다.
- **클라우드 통합**: 클라우드 환경에서 StorageClasses는 AWS EBS, Azure Disk 또는 Google Persistant Disk와 같은 클라우드 공급자로부터 특정 성능 또는 청구 특성에 따라 스토리지를 프로비저닝하는 데 사용할 수 있습니다.

pv를 자동으로 생성해주는 방식이다.
스토리지의 특성( hdd, ssd이런거 )에 따라 클래스를 정의해서 PVC가 요청할때 그에 맞는 PV를 자동으로 만들어서 갖다붙여줌


아래처럼 정의하면
``` yaml
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: storage-class-name
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iopsPerGB: "10"
  fsType: ext4
---
```

위와 연동되는 PVC 
``` yaml
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
     name: mypvc
spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 100Gi
     storageClassName: storage-class-name
---
```
이걸로 만들면스토리지 클래스가 PV를 알아서 만들어준다