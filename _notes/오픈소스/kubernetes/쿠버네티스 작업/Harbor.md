#kubernetes 
![](https://i.imgur.com/fAzcxBh.png)

##### harbor란? 
[[kubernetes]] & [[docker]]에서 사용하는 오픈소스 컨테이너 레지스트리 도구
**격리된 환경에서 직접 컨테이너 레지스트리를 구축**해야 할 니즈가 있는 경우 선택하는 도구

##### 구조
![](https://i.imgur.com/wSI1162.png)
👉`Core Service` : Harbor의 **API와 인증, 웹 GUI**를 담당하는 Component입니다. 이미지의 Pull/Push에 필요한 Token을 발행하며 웹 환경의 GUI 및 Webhook을 제공합니다.  
👉`Job Service` : 이미지의 **Replication와 같은 작업**을 담당하는 Components입니다. 이미지 Pull/Push 작업을 시작,중단,재시도하는 역할을 합니다.  
👉`Databases` : Harbor의 모든 Components들은 Stateless입니다. 때문에 저장해야 할 데이터들은 Redis와 PostgreSQL은 외부 저장소에 저장합니다. **메타데이터같은 정보들을 저장**하는 역할을 합니다. 
👉`Image Registry` : Harbor에서 실제 **컨테이너 레지스트리** 역할을 하는 Component입니다. 이미지를 저장하는 저장소 역할을 해 가져온 이미지를 저장 및 관리합니다.   
👉`Vulnerability Scanning` : Harbor는 **이미지의 취약점을 검사**하기 위한 Scanner를 제공하고 있습니다. 이에 사용되는 Vulnerability Scanner는 오픈소스 프로젝트인 Trivy와 Clair를 사용합니다.  
👉`Trusted Content` : 이미지의 Integrity와 Freshness를 증명하기 위한 **Image signing**을 맡는 Component입니다. 이를 위해 Notary라는 도구를 사용하며, 이미지의 신뢰성을 증명하기 위해 Metadata에 새긴 Sign을 관리하는 역할


2.10.0 설치
# 설치 

- [!] jenkins의 credentials와 같이 쿠버네티스에서 yaml파일을 통해 특정 pod를 생성하려고 할때는 secret을 참고하여 권한을 획득할 수 있다.
 
- [b] 스펙
스펙체크
https://goharbor.io/docs/2.10.0/install-config/installation-prereqs/
- [!] 설정
ID : admin
password : cloud1234
- [b] priviata or public 압축파일 가져오기 
 https://github.com/goharbor/harbor/releases
- [b] 설정파일 
```bash
vi harbor.yml
# Configuration file of Harbor

# The IP address or hostname to access admin UI and registry service.
# DO NOT use localhost or 127.0.0.1, because Harbor needs to be accessed by external clients.
hostname: yourdomain.com
# http related config
http: 
  # port for http, default is 80.If https enabled, this port will redirect to https port
  port: 80
# https related config # https쓰지않을거니 주석
#https:
#  # https port for harbor, default is 443
#  port: 443
#  # The path of cert and key files for nginx
#  certificate: /etc/docker/certs.d/yourdomain.com/yourdomain.com.cert
#  private_key: /etc/docker/certs.d/yourdomain.com/yourdomain.com.key
```
- [b] 설치
```bash
# 도케에서 http 접속되게 
cat > /etc/docker/daemon.json << E0F
{
   "insecure-registries": ["192.168.34.5:80"]
}
E0F

# 도커 재시작
systemctl restart docker

# 설치
./prepare
./install.sh

# 확인
docker-compose ps

# 삭제 
docker compose down -v
```
- [b] 접속
https://yourdomain.com

![500](https://i.imgur.com/rNukBpt.png)

기본 ID : admin
password는 harbor.yml에 명시했다. 
```bash
~~~
# 파일 내 기본 비밀번호 
harbor_admin_password: cloud1234
~~~
```

- [*] harbor.xml 수정시 반영 방법 
```bash
# docker 재실행
systemctl restart docker
```

## harbor에 이미지 올려보기
Projects -> liberay
![400](https://i.imgur.com/k8dNYC5.png)

Repositories에 아무것도 없는것 확인
![500](https://i.imgur.com/WWBsECM.png)

- [b] docker 에서 image push 하기 pull로 가져오기
```bash
# 먼저 docker 에 이미지를 push하기 위해 로그인
docker login 192.168.34.5:80 -u admin -p cloud1234
# Username: admin
# Password:

## docker에서 이미지 가져오기
docker pull nginx

## docker image 확인
docker images
# REPOSITORY                      TAG       IMAGE ID       CREATED      
# nginx                          22.04     174c8c134b2a   4 weeks ago   

## 새로운 harbor서버에서 docker image 등록을 위해서는 tag적용이 필요하다. 
## docker tag [IMAGE_ID] [HARBOR_URL]/[PROJECT]/[IMAGE_NAME]:[TAG]
docker tag nginx 192.168.34.5:80/library/nginx

## 새로적용한 tag의 image를 harbor서버에 push
## docker push [HARBPR_URL]/[PROJECT]/[IMAGE_NAME]:[TAG]
docker push 192.168.34.5:80/library/nginx

## 이미지 가져올때
docker pull 10.185.214.230:80/library/ubuntu
```
추가됨!
![400](https://i.imgur.com/Jy85qrD.png)
## kubernetes와 연동
- [b] [[containerd]]로 이미지레포를 사용할때, SSL 킵해야함
```bash
#기존 컨피그파일 있으면 파일 백업(파일변경)
cd /etc/containerd/ 
mv config.toml config_bkup.toml 
#컨피그파일 신규 생성 
containerd config default > config.toml

#해당하는 데이터 아래에 추가 
vi config.toml 
#시작
      [plugins."io.containerd.grpc.v1.cri".registry.configs] #여기 아래 추가1
        [plugins."io.containerd.grpc.v1.cri".registry.configs."10.185.214.230:80"]
          [plugins."io.containerd.grpc.v1.cri".registry.configs."10.185.214.230:80".tls]
            ca_file = ""
            cert_file = ""
            insecure_skip_verify = true
            key_file = ""               
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors] #여기 아래 추가2
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."10.185.214.230:80"]
          endpoint = ["http://10.185.214.230:80"]
#끝
#컨테이너D 재시작
systemctl restart containerd
```

- [b] kubernetes에서 사용하기위해 secret 설정하기 
```bash
# 쿠버네티스에서 프라이빗 이미지를 가져올때, 컨테이너 레지스트리에 인증하기 위해 `kubernetes.io/dockerconfigjso` 타입의 시크릿을 사용한다. 
kubectl create secret docker-registry harbor --docker-server=10.185.214.230 --docker-username=admin --docker-password=cloud1234

## 테스트
# .dockerconfigjson 필드는 registry 자격 증명 값의 base64 인코딩한 결과이다.
# .dockerconfigjson 필드의 값을 눈으로 볼 수 있도록 base64 decoding한다.
kubectl get secret harbor --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
# 위 출력 내용 중에서 'auth' 값을 아래와 같이 base64 decoding한다.
echo "c3R...zE2" | base64 --decode
### myusername:mypassword

# Kubernetes worker node에 Insecure registry를 등록한다.
# 만약, 10개의 node가 있다면 10개 모두 동일하게 작업해줘야 한다.
```


- [b] 만약 아래처럼 되어있으면 
![](https://i.imgur.com/VhebYQ5.png)
- [b] yaml로 경로 아래처럼 추가
```yaml
# 예제pod
apiVersion: v1
kind: Pod
metadata:
  name: foo
spec:
  containers:
    - name: foo
      image: 10.185.214.230:80/library/ubuntu
  imagePullSecrets:
  - name: harbor
```





공홈 설치
https://goharbor.io/docs/2.10.0/install-config/download-installer/
여기보고 하고있음
https://lahuman.github.io/kubernetes-harbor/



containerd 프라이빗 레지스트리 구축하는방법
https://park-hw.tistory.com/entry/%EB%8F%84%EC%BB%A4-%EB%A0%88%EC%A7%80%EC%8A%A4%ED%8A%B8-%EA%B5%AC%EC%B6%95-%EB%B0%A9%EB%B2%95