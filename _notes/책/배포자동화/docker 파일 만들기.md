[[배포 자동화 책 공부]]
## 아래 링크와 같은 내용
[[docker 이미지 만들기]]
#### 파일생성
vi Dockerfile

#### 파일내용
FROM ubuntu:20.04
RUN apt-get update && \
        apt-get install -y python

#### 이미지 생성
docker build -t ubuntu_with_python .

#### 이미지 생성 확인
docker images