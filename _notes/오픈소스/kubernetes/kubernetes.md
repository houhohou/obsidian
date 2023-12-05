#kubernetes 

👉[[컨테이너]]화 된 애플리케이션의 배포, 관리, 확장과 관련된 컨테이너에 대한 자동화를지원하는 오픈소스 [[컨테이너]] 오케스트레이션 플랫폼

##### 주요기능
- **자동 스케줄링**: 가용성을 저하시키지 않고 리소스 요구 사항 및 기타 제약 조건을 기반으로 컨테이너를 효율적으로 할당합니다.
- **자가 복구**: 실패한 컨테이너를 다시 시작하고, 노드가 종료되면 컨테이너를 교체 및 다시 예약하고, 사용자 정의 상태 확인에 응답하지 않는 컨테이너를 종료하고, 서비스할 준비가 될 때까지 클라이언트에 알리지 않습니다.
- **수평 확장**: 간단한 명령, UI를 사용하거나 CPU 사용량에 따라 자동으로 애플리케이션을 확장 및 축소합니다.
- **서비스 검색 및 로드 밸런싱**: Kubernetes는 DNS 이름이나 IP 주소를 사용하여 컨테이너를 노출할 수 있습니다. 컨테이너에 대한 트래픽이 높으면 Kubernetes는 네트워크 트래픽의 부하를 분산하고 분산할 수 있습니다.


## 리소스
⭐쿠버네티스를 관리하기 위해 등록하는 **리소스**

👉 종류 

✅ **워크로드 API 카테고리**
		'컨테이너 가동'하기 위해 관련된 리소스
				[[Pod]] [[ReplicationController]] [[ReplicaSet]] [[deployment]] [[DaemonSet]]  [[StatefulSet]] [[Job]] [[Cronjob]]
✅ **서비스 API 카테고리**
		'엔드포인트' 관련 리소스
				- 서비스 
				[[ClusterIP]] [[ExternalIP]] [[NodePort]] [[LoadBalancer]] [[Headless]] [[ExternalName]] [[None-Selctor]]
				- 인그레스
✅ **컨피그 & 스토리지  API 카테고리**
		설정/기밀 정보/영구 볼륨 관련 리소스
				[[Secret]] [[Configmap]] [[PVC]]
✅ **클러스터 API 카테고리**
		보안이나 쿼터 관련 리소스
				[[워커노드]] [[namespace]] [[PV]] [[ResourceQuota]] [[ServiceAccount]] [[Role]] [[ClusterRole]] [[RoleBinding]] [[ClusterRoleBinding]] [[NetworkPolicy]]
✅ **메타데이터  API 카테고리**
		클러스터 내부의 다른 리소스 관리하기 위한 리소스
				[[LimitRange]] [[HPA]] [[PDB]] [[CustomResourceDefinition]]
			