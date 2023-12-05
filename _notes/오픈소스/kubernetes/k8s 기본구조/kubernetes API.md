#kubernetes 
[[kubernetes]] 클러스터를 위한 다양한 API객체를 노출하는 http기반의 REST 서버
[[kubectl]]이 API Server에 CLI 요청을 보내 정보를 조회하거나 변경을 함. 
![](https://i.imgur.com/UuG7bWU.png)

##### 인증방식
외부 :  Master Node의 API Server에 접근 시 HTTPS 프로토콜 인증서 필요
내부 : 인증서 없이 HTTP로 접근가능
##### API 객체가 갖는 항목
1. 명명된 API 버전 
	- vi 
	- rbac.authorization.k8s.io/v1
2. 종류 
	- ex) kind: Deployment
3. 메타데이터 섹션

##### API서버 구성
- **API 관리** - 서버에서 API를 노출하고 관리하는 프로세스
- **요청 처리** - 클라이언트의 개별 API 요청을 처리하는 가장 큰 기능 집합
- **내부 제어 루프** - API 서버의 성공적인 작동에 필요한 백그라운드 작업을 담당하는 안쪽 부분
##### API 요청 처리
- **GET** - 특정 리소스와 연관된 데이터를 검색합니다.
- **LIST** - Collection GET , LIST 여러 가지 요청을 나열하는 요청입니다.
- **POST** - 리소스를 생성하기 위해 POST 요청을 사용합니다. 요청 메세지 본문에는 새로 만들어야 하는 리소스를 넣습니다.
- **DELETE** - 리소스를 삭제하는 요청입니다.

***현재  클러스터에  있는 api 타입 확인

```/bin/bash
kubectl api-resources | head
```

![](https://i.imgur.com/Dw3xYok.png)

클러스터 정보

**인증 플러그인으로 클라이언트 인증**
먼저 API Server는 요청을 보낸 client를 인증해야 한다. 이 작업은 API Server에 구성된 하나 이상의 plugin들이 수행하며, API 서버는 누가 요청을 보낸 것인지 결정할 수 있을 때까지 이 plugins들을 차례로 호출한다. 그리고 이는 HTTP 요청을 검사해 수행한다.

인증 방법에 따라 client를 인증서 혹은 HTTP header에서 가져오며, plugin은 클라이언트의 사용자 이름, 사용자 ID, 속해있는 그룹 정보를 추출한다. 그리고 이 데이터는 다음 단계인 인가 단계에서 사용된다.

## API 서버 정보
현재 API서버 주소를 알아보려면  아래를 입력한다
```bash
kubectl cluster-info
```
![](https://i.imgur.com/WGpy9og.png)



정보가 나오게 되는데 보통 아래에 저장된다

```bash
#둘다 같은 파일 같은곳에 저장됨
~/.kube/config
/etc/kubernetes/admin.conf
```

변경하고 싶으면 아래처럼 변경가능
```bash
export KUBECONFIG=<kube_conf>
```

## API 서버 호출

프록시로 바운딩

``` bash
kubectl proxy &
```
![](https://i.imgur.com/iQhmauc.png)


웹브라우저로 들어가보면
![](https://i.imgur.com/04SZeHr.png)

위처럼 뜬다. 그럼 아무거나덧붙여서 확인해보면 아래처럼 접근가능

![](https://i.imgur.com/eAJBln0.png)


커피고래의 kube-api 
https://coffeewhale.com/apiserver