![100](https://i.imgur.com/4pfBbjA.png)

오픈소스로 priviate 환경에서 구성가능한 git 서비스. 

특징
1. 경량성과 효율성 : Go 언어로 작성되어, 상대적으로 적은 자원으로 빠르고 효율적으로 실행
2. 쉬운 설치 및 설정 : 설치간단, 다양한 운영체제에서 쉽게 설정가능
3. 사용자 친화적인 인터페이스 : GitHub와 유사한 사용자 인터페이스 제공
4. 자체 호스팅 : 개인서버나 클라우드에 설치해서 사용가능
5. 커뮤니티 기반 : 오픈소스 프로젝트

#### kubernetes에 설치
```bash
helm repo add gitea-charts https://dl.gitea.com/charts/
helm pull gitea-charts/gitea --untar
helm install gitea ./gitea -n default
```
![400](https://i.imgur.com/hicZdaN.png)



















