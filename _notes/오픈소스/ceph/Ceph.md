#리눅스 

![](https://i.imgur.com/xVClFrP.png)


**Ceph** 는 분산 저장 시스템으로, 대규모 데이터를 안정적이고 확장 가능하게 저장하고 관리할 수 있는 오픈 소스 소프트웨어.  `객체 스토리지`, `블록 스토리지` 등을 제공하여 데이터의 저장및 관리가 용이하며, 다수의 노드에 걸쳐 데이터를 분산시켜 `안정성`을 확보

**Ceph-Ansible**은 Ceph 클러스터를 구축하고 유지보수하기 위한 도구입니다. yaml 파일형식을 사용하여 다수의 서버를 설정하고 관리할 수 있는 오픈 소스 도구이며 이를 사용해 빠르고 효율적으로 Ceph 클러스터를 배포할 수 있습니다.

### Ceph 구성요소

1. **Monitor (mon):**
    - Monitor는 클러스터 상태와 매핑 정보를 유지하고 다른 Ceph 데몬과의 상호 작용합니다. 고가용성 구성을 위해 최소 3개의 Monitor가 필요합니다.
2. **Manager (mgr):**
    - Manager는 클러스터의 상태, 성능 통계 및 모니터링 정보를 수집하고 표시합니다.
3. **Object Storage Daemon (OSD):**
    - 클러스터의 실제 데이터를 저장하고 관리합니다. 데이터를 분산 저장하고, 복제 및 데이터 회복을 담당하여 데이터의 안정성과 가용성을 보장합니다.
    - OSD 디스크 수는 최소 3의 배수로 설정하는 것이 좋습니다.
4. **RADOS (Reliable Autonomic Distributed Object Store):**
    - Ceph의 데이터 접근에 근간을 이루는 서비스
    - `객체기반`으로 설계
    - Object Storage, Block Storage, File Storage 인터페이스 제공
    - Ceph에서 객체를 일고 쓸때 사용
1. **CephFS (Ceph File System):**
    - CephFS는 Ceph의 파일 시스템으로, 분산된 클라우드 환경에서 파일을 공유하고 관리할 수 있는 기능을 제공합니다.





- [!]  참고 블로그
https://dogfootlog.tistory.com/4