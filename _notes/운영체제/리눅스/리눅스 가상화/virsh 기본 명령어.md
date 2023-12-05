
[[리눅스]] 가상화 기술인 [[virsh]] 기본 명령어

##### 인스턴스 확인명령어
```bash
#가동중인 인스턴스 확인
virsh list

#전체 인스턴스 확인하기
virsh list --all

#인스턴스 상세확인
virsh edit [인스턴스 이름]

#인스턴스 상태 확인 (CPU 같은거 확인)
virsh domstats [가상머신도메인 이름]
```


##### 인스턴스 동작 설정
```bash
#인스턴시 켜기 
virsh start [인스턴스 이름]

#인스턴스 일시중지 ⏸️
virsh suspend [인스턴스 이름]

#인스턴스 일시중지 중 시작
virsh resume [인스턴스 이름]

#인스턴스 재부팅
virsh reboot [인스턴스 이름]

#인스턴스 끄기
virsh shutdown [인스턴스 이름]

#인스턴스 삭제 ❌
virsh destroy [인스턴스 이름]
```

##### 인스턴스의 스냅샷
``` bash
#인스턴스 스냅샷 찍기
virsh snapshot-create-as --domain [가상머신도메인 이름] [스냅샷이름]

#인스턴스 스냅샷 리스트
virsh snapshot-list --domain [가상머신도메인 이름]

#인스턴스 스냅샷 복원
virsh snapshot-revert [가상머신도메인 이름] [스냅샷이름]

#인스턴스 스냅샷 삭제
virsh snapshot-delete --domain [가상머신도메인 이름] [스냅샷이름]

#인스턴스 환경 저장
virsh save  <instance_name> example_file.xml

#인스턴스 환경파일로 시작
virsh restore example_file.xml
```

##### 인스턴스 접속
``` bash
#콘솔 접속
virsh console <instance_name>

#콘솔 접속
virsh console <instance_id>

#콘솔 빠져나오기
## 키보드의 ctrl + ] 를 누른다
```