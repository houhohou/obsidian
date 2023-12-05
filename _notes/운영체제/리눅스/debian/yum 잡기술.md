[[debian]] 에서 설치하는 명령어인 yum에 대한 명령어
##### 종속성까지 모두 다운로드 
#주의 이미 설치된 종속성 패키지는 안받아옴!
```bash
#dnf 호환버전설치되게 변경
dnf distro-sync --nobest

#종속성까지 모두 rpm만 다운로드 
yumdownloader --resolve [패키지명]
```
