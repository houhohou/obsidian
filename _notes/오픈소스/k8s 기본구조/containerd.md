#kubernetes 
![](https://i.imgur.com/sm1uLl1.png)


✅ 업계 표준 핵심 '컨테이너 런타임'
✅ 시스템 내 컨테이너의 전체 수명주기를 관리하는 역할을 담당

1. **컨테이너 수명주기 관리**: 'containerd'는 컨테이너 생성, 시작, 중지, 삭제를 포함하여 컨테이너의 전체 수명주기를 관리합니다. 컨테이너 프로세스의 실행 및 감독을 처리
2. **이미지 관리**: 컨테이너 레지스트리에서 이미지를 가져오고, 로컬 이미지 스토리지를 관리하고, 컨테이너에서 사용할 수 있는 파일 시스템에 이미지 압축을 푸는 일을 담당
3. **스토리지 및 볼륨 관리**: 'containerd'는 컨테이너의 스토리지 계층을 관리하고 볼륨 마운트 및 마운트 해제와 같은 작업을 처리하며 스토리지 드라이버를 관리
4. **네트워킹**: 'containerd' 자체는 네트워킹을 직접 처리하지는 않지만 외부 플러그인이나 에이전트가 컨테이너에 네트워크 구성을 적용하는 데 필요한 후크를 제공
5. **실행 및 격리**: `containerd`는 `runc`(기본값)와 같은 런타임을 사용하여 컨테이너를 실행합니다. 이러한 런타임은 네임스페이스 및 cgroup과 같은 Linux 기능을 사용하여 컨테이너 간, 컨테이너와 호스트 시스템 간 격리
6. **보안**: 안전한 가져오기, TLS를 사용하여 레지스트리와 통신, 사용자 네임스페이스 재매핑 지원을 포함하되 이에 국한되지 않고 컨테이너 보안을 강화하는 다양한 기능을 지원
7. **상호 운용성**: 핵심 구성 요소인 'containerd'는 더 큰 시스템에 내장되도록 설계되었습니다. 다른 도구 및 시스템과의 통합을 위한 강력하고 안정적인 고급 API를 제공합니다. 이로 인해 Kubernetes를 포함한 다양한 플랫폼에서 널리 사용
8. **효율성 및 성능**: 'containerd'는 가볍고 효율적으로 설계되어 더 적은 리소스를 소비하면서도 컨테이너를 효과적으로 관리하는 데 필요한 기능을 제공