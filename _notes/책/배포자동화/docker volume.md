[[배포 자동화 책 공부]]
#### 컨테이너 실행, 파일 생성
docker run -i -t -v ~/docker_ubuntu:/host_directory ubuntu:20.04 /bin/bash
touch /host_directory/file.txt
exit

ls ~/docker_ubuntu

![](https://i.imgur.com/R5c9Jl7.png)


#### 컨테이너 삭제 후 재실행, 디렉토리 내 파일 확인
docker stop 942798c8ec1e
docker run -i -t -v ~/docker_ubuntu:/host_directory ubuntu:20.04 /bin/bash
ls /host_directory/

![](https://i.imgur.com/1KXA4jE.png)
