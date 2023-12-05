#kubernetes 
[[kubernetes]]에서 사용자가 요청한 볼륨 
[[PV]]리소스를 소비하며 할당함

pvc는 [[namespace]] 종속이기에 지정해줘야함

## 특징
- **스토리지 소비**: 사용자는 스토리지 인프라의 기본 세부 정보를 알지 못한 채 스토리지 리소스를 요청
- **바인딩**: PV에 대한 PVC는 바인딩입니다. 이 바인딩은 배타적이므로 PV는 하나의 PVC에만 바인딩될 수 있습니다.
- **동적 프로비저닝**: 사용자가 PVC를 요청하면 Kubernetes는 사용자 기준을 충족하는 기존 PV가 없는 경우 해당 PV를 동적으로 프로비저닝하여 요청을 충족할 수 있습니다.

### PV & PVC 관계
- 사용자가 영구 저장소를 사용하려는 경우 크기와 액세스 모드 및 저장소 클래스를 지정하는 PVC를 만듭니다. 그런 다음 Kubernetes는 적절한 PV를 찾아서 함께 바인딩합니다.
- 적합한 PV를 사용할 수 없고 [[Storage Class]]가 허용하는 경우 클러스터는 PVC 수요를 충족하기 위해 새 PV를 동적으로 프로비저닝할 수 있습니다.
- 이 모델은 스토리지가 제공되는 방식과 소비되는 방식에 대한 세부 사항을 추상화하여 유연성과 사용 편의성을 제공합니다.

기본 pvc 볼륨

```yaml
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-name
spec:
  storageClassName: manual
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce #타입 정함 ( ReadWriteOnly , ReadWriteOnce )
  resources:
    requests:
      storage: 10Gi
---
```



pvc를 사용하는 pod를 쓰기위해서는

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:                      #------------------
      - mountPath: "/var/www/html"       # 여기구간은 컨테이너가 볼륨에마운트 되는 구간지정
        name: mypd                       #------------------
  volumes:                               #------------------
    - name: mypd                         # 여기 구간은 볼륨이 파드와 
      persistentVolumeClaim:             # 마운트 된다는 구간 지정
        claimName: pvc-name #PVC이름      #------------------
---

```