---
tistoryBlogName: sangmok9
tistoryTitle: Ovirt
tistoryTags: Ovirt
tistoryVisibility: "3"
tistoryCategory: "972478"
tistorySkipModal: true
tistoryPostId: "185"
tistoryPostUrl: https://sangmok9.tistory.com/185
---
#ovirt #debian

여러개의 [[가상머신]]을 잘 관리하면서 끊김없이 지속적으로 서비스를 하기 위한 [[libvirt]]기반 중앙 집중형 오픈소스 가상화 플랫폼
#### 특징
- KVM 하이퍼바이저를 기반으로 구축
- 실시간 migration 지원
- VM 리소스 사용량 모니터링
#### 기능들
- 하드웨어 파워 관리
- 스토리지, 네트워크 자원 관리
- 가상머신 배포,관리
- 마이그레이션, 고가용성
- 시스템 스케줄링 및 하이퍼바이저 관리
- 각 노드와 전체 플랫폼 모니터링
#### 구성요소
- ovirt 엔진 : 'GUI'기반 웹서비스고, 'REST API' 를 이용해 데이터센터  구성요소 관리, 콘솔 연결시 SPICE라는 프로토콜로 통신이 이루어짐


		Standalone Engine
![](https://i.imgur.com/aYjuIyt.png)

				Self-hosted
![](https://i.imgur.com/pAe8KIM.png)



- host : 엔터프라이즈 리눅스와 ovirt 노드 두가지 타입의 호스트가 있다, 호스트는 KVM을 사용해 가상머신 동작을 위한 리소스를 분배하는 역할을 한다. 그래서 hypervisor를 호스트에 올린다

- shared strage : 가상머신에서 생성된, 그리고 가상머신과 관련 데이터들이 저장될 장소

- data warehouse : ovirt engine 설정정보와 통계 데이터를 기록할 공간

- network : 네트워크 담당











레포지터리 구성
https://resources.ovirt.org/pub/yum-repo/


참조 블로그
https://blog.naver.com/PostView.nhn?blogId=ilikebigmac&logNo=222013980565



4.4.10 설치 됨
https://www.server-world.info/en/note?os=CentOS_8&p=ovirt44&f=1

