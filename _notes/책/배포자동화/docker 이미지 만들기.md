[[배포 자동화 책 공부]]


#### 우분투 컨테이너 실행하고 접속
docker run -i -t ubuntu:20.04 /bin/bash
#### git 설치
apt-get update
apt-get install -y git

#### 설치 확인
which git

exit

#### ubuntu:20.04이미지와 바뀐점 확인
docker diff 889bb5cd66c3

#### 이미지 생성
docker commit 889bb5cd66c3 ubuntu_with_git

#### 이미지 확인
docker images