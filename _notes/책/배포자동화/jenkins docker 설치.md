[[배포 자동화 책 공부]]
##### 디렉토리 생성, 권한 변경
mkdir ~/j1
sudo chown -R 1000:1000 ~/j1

##### 젠킨스 컨테이너 생성
sudo docker run -p 60001:8080 -v ~/j1:/var/jenkins_home jenkins/jenkins

###### 접속
포트로 접속하면, 패스워드 입력하라 나오는데, docker 컨테이너 올라가면서 패스워드 스쳐지나감 그거 입력하면됨
![](https://i.imgur.com/OVpzADL.png)


권장설치하면 알아서 설치됨

![](https://i.imgur.com/YHu5hq3.png)

이름 그런거 입력하면
![](https://i.imgur.com/5CXAv7w.png)





