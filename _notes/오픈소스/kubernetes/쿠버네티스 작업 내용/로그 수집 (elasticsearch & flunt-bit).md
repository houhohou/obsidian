#kubernetes 
### 원리 & 종류
/var/log/pods, /var/log/container이 따로 남는 로그들을 통합해서 관리해야한다. 이를 위한 방식

- ELK stack(Elasticserach 검색엔진 Logstach 로그 수집기 Kibana 데이터 시각화): java로 만들어져 있고 로그수집기가 무거움.

- EFL stack(Elasticserach Fluentd Kibana): 제일 선호하는 방식, CNCF 프로젝트이고 쿠버네티스에서는 C,C++로 만들어진 이 로그수집기를 사용한다.  
    가공과 필터기능이 있는데 이를 많이 제거해서 **경량화 시킨것이 FluentBit**이다.

#로그수집기 로그를 긁어서 가져옴
#Elasticsearch 저장한 데이터를 검색
쿼리문을 통해 프로메테우스로 정보 가져오고 kibana로 보기 쉽게 만듦


	설치
```bash
#레포 추가 
helm repo add elastic https://helm.elastic.co
helm repo update

#Pull
helm pull elastic/elasticsearch --untar

#설치
helm install elasticsearch ./elasticsearch --version 7.17.3  -n logging
```





	레포 추가
```bash
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
```
설치
```bash
helm pull fluent/fluent-bit --untar
helm install fluent-bit ./fluent-bit -n logging
```



```bash

```


```bash

```


```bash

```


```bash

```


```bash

```




https://velog.io/@hsshin0602/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-HELM