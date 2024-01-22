#kubernetes 
![](https://i.imgur.com/MxopVbb.png)

Jaeger를 dkfd 

## 트레이싱

**트레이싱 ( tracing )이란?**
Service Mesh 안에서 요청 tracing이 화자되며, 각 서버간의 요청 관계가 복잡해질수록 병목지점을 찾아내는게 어려워진다. 트레이싱은 이를 해결하기 위해 가시성을 제공해준다. 또한 어느 경우에 레이턴시가 튀는지 확인하기 위한 용도로 쓰일수 있다. 

즉 주요 목적은
- HTTP, gRPC 등의 프로토콜을 사용하는 서버 간의 요청 추적 (ex. 어느 서버에서 얼마나 시간이 소요되었나?)
- 서버 내부 프로세싱의 추적 (ex. 어떤 함수에서 시간이 얼마나 많이 소요되었으며, 얼마나 많이 수행하였나?)

## Jaeger 
 Micro Service간의 트랜잭션을 모니터링하고 문제를 해결하는데 사용할 수 있는 오픈소스.
 Micro Service특징이, 요청을 보내고 여러구간을 거쳐 받는 과정에서 log들이 분산되어 있고, 각 서버간의 요청 관계가 복잡해질수록 장애 및 병목지점을 찾아내는게 어려워지는데, 이를 모니터링해서 어느구간의 문제인지 잡아내기위한 기능
 
![500](https://i.imgur.com/GKSNjZG.png)
#### Span ( 스팬 )
- 분산 추척에서는 가장 기본이 되는 블록 단위로 분산 시스템에서 실행되는 작업 단위
	 - ex) HTTP request, call to DB
- Span 구성요소
    - 작업 이름
    - 시작 시간과 중지
    - 시간개발자가 Span 을 분석하는 데 도움이 되는 태그 또는 값
    - Micro service 가 생성하는 메시지를 저장하는 로그
    - Span 컨텍스트 또는 Span 에 대한 추가 설명
#### Trace ( 추적 )
- Trace는 시스템 전반에서 데이터/실행 경로를 나타냄
- 1개 이상의 Span으로 이루어져 있고 여러 개의 Span이 모여서 하나의 Trace를 완성하게 되며, 특정 시간 동안 발생하는 이벤트를 나타냄
- Trace를 이용해서 어플리케이션이 어떤 경로를 이용해 이벤트끼리 상호작용하고, 얼마만큼의 레이턴시를 가지며, 어떤 부분에서 문제가 발생할 수 있는지 파악가능


## 설치
#### 주의사항
- istio가 설치되어 있어야 함
- Cert-manager가 설치되어 있어야 함
- All-In-One으로 사용해도 되지만 본 메뉴얼에서는 collector + query + agent 방식으로 사용


![100](https://i.imgur.com/JVsKDkS.png)
##### CertManager Install
**Cert-manager** : Kubernetes 내부에서 HTTPS 통신을 위한 인증서를 생성하고, 또 인증서의 만료 기간이 되면 자동으로 인증서를 갱신해주는 역할을 하는 Certificate manager controller


## tjfcl
- [b] 
```bash
# helm 레포 추가
helm repo add cert-manager https://charts.jetstack.io

# helm repo pull
helm pull cert-manager/cert-manager --version 1.12.2
tar -xvf cert-manager-v1.12.2.tgz

# values 수정
vi cert-manager/values.yaml
# 80번줄 image: repository: 레포 경로 수정
# 413번줄 image: repository: 레포 경로 수정
# 572번줄 image: repository: 레포 경로 수정
# 613번줄 image: repository: 레포 경로 수정
```

|Parameter|Description|Default|
|---|---|---|
|global.imagePullSecrets|이미지를 가져올 때 사용할 하나 이상의 비밀 참조. |[]|
|global.commonLabels|모든 리소스에 적용할 레이블 |{}|
|global.rbac.create|참이면 RBAC 리소스를 생성하고 사용(하위 차트 포함) |true|
|global.priorityClassName|cert-manager 및 webhook 파드의 우선 순위 클래스 이름 |""|
|global.podSecurityPolicy.enabled|참이면 PodSecurityPolicy를 생성하고 사용(하위 차트 포함). |false|
|global.podSecurityPolicy.useAppArmor|참이면 PSP에서 Apparmor seccomp 프로필을 사용 |true|
|global.leaderElection.namespace|리더 선출을 위해 ConfigMap을 저장하는 데 사용되는 네임스페이스를 재정의 |kube-system|
|global.leaderElection.leaseDuration|비리더 후보가 리더십 갱신을 관찰한 후 리더십을 획득하기 위해 시도하기 전까지 대기하는 기간. ||
|global.leaderElection.renewDeadline|리더가 리더십 슬롯을 갱신하는 데 시도할 간격 ||
|global.leaderElection.retryPeriod|리더십 획득 및 갱신을 시도하기 전에 클라이언트가 대기해야 하는 기간. ||
|installCRDs|참이면 CRD 리소스가 Helm 차트의 일부로 설치. 활성화하면 삭제 시 CRD 리소스가 삭제되어 모든 설치된 사용자 정의 리소스가 삭제 |false|
|image.repository|이미지 저장소 |[![20](https://quay.io/static/img/quay_favicon.png)Quay](http://quay.io/jetstack/cert-manager-controller) |
|image.tag|이미지 태그 |v1.12.2|
|image.pullPolicy|이미지 가져오기 정책 |IfNotPresent|
|replicaCount|cert-manager 복제본 수 |1|
|clusterResourceNamespace|ClusterIssuer 리소스에 대해 DNS 공급자 자격 증명 등을 저장하는 데 사용되는 네임스페이스를 재정의 |Same namespace as cert-manager pod|
|featureGates|컨트롤러에 대한 기능 게이트를 설명하는 쉼표로 구분된 key=value 쌍 집합 |``|
|extraArgs|cert-manager에 대한 선택적 플래그. |[]|
|extraEnv|cert-manager에 대한 선택적 환경 변수. |[]|
|serviceAccount.create|참이면 새 서비스 계정을 생성 |true|
|[serviceAccount.name](http://serviceaccount.name/ "http://serviceAccount.name")|사용할 서비스 계정. 설정되지 않았고 serviceAccount.create가 참이면 fullname 템플릿을 사용하여 이름이 생성 ||
|serviceAccount.annotations|서비스 계정에 추가할 주석 ||
|serviceAccount.automountServiceAccountToken|서비스 계정에 대한 API 자격 증명 자동 마운트 |true|
|volumes|cert-manager에 대한 선택적 볼륨 |[]|
|volumeMounts|cert-manager에 대한 선택적 볼륨 마운트 |[]|
|resources|CPU/메모리 리소스 요청/제한 |{}|
|securityContext|컨트롤러 파드 할당을 위한 보안 컨텍스트 |refer to Default Security Contexts|
|containerSecurityContext|컨트롤러 구성 요소 컨테이너에 설정할 보안 컨텍스트 |refer to Default Security Contexts|
|nodeSelector|파드 할당을 위한 노드 레이블 |{}|
|affinity|파드 할당을 위한 노드 친화성 |{}|
|tolerations|파드 할당을 위한 노드 허용 |[]|
|topologySpreadConstraints|파드 할당을 위한 토폴로지 확산 제약 조건. |[]|
|livenessProbe.enabled|컨트롤러 컨테이너에 대한 생존 탐사기 활성화 또는 비활성화 |false|
|livenessProbe.initialDelaySeconds|생존 탐사기 초기 지연 시간(초) |10|
|livenessProbe.periodSeconds|생존 탐사기 기간(초). |10|
|livenessProbe.timeoutSeconds|생존 탐사기 시간 초과(초) |10|
|livenessProbe.periodSeconds|생존 탐사기 기간(초). |10|
|livenessProbe.successThreshold|생존 탐사기 성공 임계값 |1|
|livenessProbe.failureThreshold|생존 탐사기 실패 임계값 |8|
|ingressShim.defaultIssuerName|인그레스 리소스에 사용할 선택적 기본 발급자 ||
|ingressShim.defaultIssuerKind|인그레스 리소스에 사용할 선택적 기본 발급자 종류 ||
|ingressShim.defaultIssuerGroup|인그레스 리소스에 사용할 선택적 기본 발급자 그룹 ||
|prometheus.enabled|프로메테우스 모니터링 활성화 |true|
|prometheus.servicemonitor.enabled|프로메테우스 운영자 ServiceMonitor 모니터링 활성화 |false|
|prometheus.servicemonitor.namespace|ServiceMonitor 리소스를 배포할 네임스페이스 정의 |(namespace where you are deploying)|
|prometheus.servicemonitor.prometheusInstance|프로메테우스 인스턴스 정의 |default|
|prometheus.servicemonitor.targetPort|프로메테우스 스크레이프 포트 |9402|
|prometheus.servicemonitor.path|프로메테우스 스크레이프 경로 |/metrics|
|prometheus.servicemonitor.interval|프로메테우스 스크레이프 간격 |60s|
|prometheus.servicemonitor.labels|ServiceMonitor에 추가할 사용자 정의 ||
|prometheus.servicemonitor.scrapeTimeout|Prometheus scrape timeout|30s|
|prometheus.servicemonitor.honorLabels|Enable label honoring for metrics scraped by Prometheus (see [![](https://prometheus.io/assets/favicons/favicon-16x16.png)Configuration \| Prometheus](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)  for details). By setting honorLabels to true, Prometheus will prefer label contents given by cert-manager on conflicts. Can be used to remove the "exported_namespace" label for example.|false|
|podAnnotations|Annotations to add to the cert-manager pod|{}|
|deploymentAnnotations|Annotations to add to the cert-manager deployment|{}|
|podDisruptionBudget.enabled|Adds a PodDisruptionBudget for the cert-manager deployment|false|
|podDisruptionBudget.minAvailable|Configures the minimum available pods for voluntary disruptions. Cannot used if maxUnavailable is set.|1|
|podDisruptionBudget.maxUnavailable|Configures the maximum unavailable pods for voluntary disruptions. Cannot used if minAvailable is set.||
|podDnsPolicy|Optional cert-manager pod(https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pods-dns-policy) ||
|podDnsConfig|Optional cert-manager pod(https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pods-dns-config) ||
|podLabels|Labels to add to the cert-manager pod|{}|
|serviceLabels|Labels to add to the cert-manager controller service|{}|
|serviceAnnotations|Annotations to add to the cert-manager service|{}|
|http_proxy|Value of the HTTP_PROXY environment variable in the cert-manager pod||
|https_proxy|Value of the HTTPS_PROXY environment variable in the cert-manager pod||
|no_proxy|Value of the NO_PROXY environment variable in the cert-manager pod||
|dns01RecursiveNameservers|Comma separated string with host and port of the recursive nameservers cert-manager should query|``|
|dns01RecursiveNameserversOnly|Forces cert-manager to only use the recursive nameservers for verification.|false|
|enableCertificateOwnerRef|When this flag is enabled, secrets will be automatically removed when the certificate resource is deleted|false|
|webhook.replicaCount|Number of cert-manager webhook replicas|1|
|webhook.timeoutSeconds|Seconds the API server should wait the webhook to respond before treating the call as a failure.|10|
|webhook.podAnnotations|Annotations to add to the webhook pods|{}|
|webhook.podLabels|Labels to add to the cert-manager webhook pod|{}|
|webhook.serviceLabels|Labels to add to the cert-manager webhook service|{}|
|webhook.deploymentAnnotations|Annotations to add to the webhook deployment|{}|
|webhook.podDisruptionBudget.enabled|Adds a PodDisruptionBudget for the cert-manager deployment|false|
|webhook.podDisruptionBudget.minAvailable|Configures the minimum available pods for voluntary disruptions. Cannot used if maxUnavailable is set.|1|
|webhook.podDisruptionBudget.maxUnavailable|Configures the maximum unavailable pods for voluntary disruptions. Cannot used if minAvailable is set.||
|webhook.mutatingWebhookConfigurationAnnotations|Annotations to add to the mutating webhook configuration|{}|
|webhook.validatingWebhookConfigurationAnnotations|Annotations to add to the validating webhook configuration|{}|
|webhook.serviceAnnotations|Annotations to add to the webhook service|{}|
|webhook.config|WebhookConfiguration YAML used to configure flags for the webhook. Generates a ConfigMap containing contents of the field. See values.yaml for example.|{}|
|webhook.extraArgs|Optional flags for cert-manager webhook component|[]|
|webhook.serviceAccount.create|If true, create a new service account for the webhook component|true|
|[webhook.serviceAccount.name](http://webhook.serviceaccount.name/ "http://webhook.serviceAccount.name")|Service account for the webhook component to be used. If not set and webhook.serviceAccount.create is true, a name is generated using the fullname template||
|webhook.serviceAccount.annotations|Annotations to add to the service account for the webhook component||
|webhook.serviceAccount.automountServiceAccountToken|Automount API credentials for the webhook Service Account||
|webhook.resources|CPU/memory resource requests/limits for the webhook pods|{}|
|webhook.nodeSelector|Node labels for webhook pod assignment|{}|
|webhook.networkPolicy.enabled|Enable default network policies for webhooks egress and ingress traffic|false|
|webhook.networkPolicy.ingress|Sets ingress policy block. See NetworkPolicy documentation. See values.yaml for example.|{}|
|webhook.networkPolicy.egress|Sets ingress policy block. See NetworkPolicy documentation. See values.yaml for example.|{}|
|webhook.affinity|Node affinity for webhook pod assignment|{}|
|webhook.tolerations|Node tolerations for webhook pod assignment|[]|
|webhook.topologySpreadConstraints|Topology spread constraints for webhook pod assignment|[]|
|webhook.image.repository|Webhook image repository|[![](https://quay.io/static/img/quay_favicon.png)Quay](http://quay.io/jetstack/cert-manager-webhook)|
|webhook.image.tag|Webhook image tag|v1.12.2|
|webhook.image.pullPolicy|Webhook image pull policy|IfNotPresent|
|webhook.securePort|The port that the webhook should listen on for requests.|10250|
|webhook.securityContext|Security context for webhook pod assignment|refer to Default Security Contexts|
|webhook.containerSecurityContext|Security context to be set on the webhook component container|refer to Default Security Contexts|
|webhook.hostNetwork|If true, run the Webhook on the host network.|false|
|webhook.serviceType|The type of the Service.|ClusterIP|
|webhook.loadBalancerIP|The specific load balancer IP to use (when serviceType is LoadBalancer).||
|webhook.url.host|The host to use to reach the webhook, instead of using internal cluster DNS for the service.||
|webhook.livenessProbe.failureThreshold|The liveness probe failure threshold|3|
|webhook.livenessProbe.initialDelaySeconds|The liveness probe initial delay (in seconds)|60|
|webhook.livenessProbe.periodSeconds|The liveness probe period (in seconds)|10|
|webhook.livenessProbe.successThreshold|The liveness probe success threshold|1|
|webhook.livenessProbe.timeoutSeconds|The liveness probe timeout (in seconds)|1|
|webhook.readinessProbe.failureThreshold|The readiness probe failure threshold|3|
|webhook.readinessProbe.initialDelaySeconds|The readiness probe initial delay (in seconds)|5|
|webhook.readinessProbe.periodSeconds|The readiness probe period (in seconds)|5|
|webhook.readinessProbe.successThreshold|The readiness probe success threshold|1|
|webhook.readinessProbe.timeoutSeconds|The readiness probe timeout (in seconds)|1|
|cainjector.enabled|Toggles whether the cainjector component should be installed (required for the webhook component to work)|true|
|cainjector.replicaCount|Number of cert-manager cainjector replicas|1|
|cainjector.podAnnotations|Annotations to add to the cainjector pods|{}|
|cainjector.podLabels|Labels to add to the cert-manager cainjector pod|{}|
|cainjector.deploymentAnnotations|Annotations to add to the cainjector deployment|{}|
|cainjector.podDisruptionBudget.enabled|Adds a PodDisruptionBudget for the cert-manager deployment|false|
|cainjector.podDisruptionBudget.minAvailable|Configures the minimum available pods for voluntary disruptions. Cannot used if maxUnavailable is set.|1|
|cainjector.podDisruptionBudget.maxUnavailable|Configures the maximum unavailable pods for voluntary disruptions. Cannot used if minAvailable is set.||
|cainjector.extraArgs|Optional flags for cert-manager cainjector component|[]|
|cainjector.serviceAccount.create|If true, create a new service account for the cainjector component|true|
|[cainjector.serviceAccount.name](http://cainjector.serviceaccount.name/ "http://cainjector.serviceAccount.name")|Service account for the cainjector component to be used. If not set and cainjector.serviceAccount.create is true, a name is generated using the fullname template||
|cainjector.serviceAccount.annotations|Annotations to add to the service account for the cainjector component||
|cainjector.serviceAccount.automountServiceAccountToken|Automount API credentials for the cainjector Service Account|true|
|cainjector.resources|CPU/memory resource requests/limits for the cainjector pods|{}|
|cainjector.nodeSelector|Node labels for cainjector pod assignment|{}|
|cainjector.affinity|Node affinity for cainjector pod assignment|{}|
|cainjector.tolerations|Node tolerations for cainjector pod assignment|[]|
|cainjector.topologySpreadConstraints|Topology spread constraints for cainjector pod assignment|[]|
|cainjector.image.repository|cainjector image repository|[![](https://quay.io/static/img/quay_favicon.png)Quay](http://quay.io/jetstack/cert-manager-cainjector)|
|cainjector.image.tag|cainjector image tag|v1.12.2|
|cainjector.image.pullPolicy|cainjector image pull policy|IfNotPresent|
|cainjector.securityContext|Security context for cainjector pod assignment|refer to Default Security Contexts|
|cainjector.containerSecurityContext|Security context to be set on cainjector component container|refer to Default Security Contexts|
|acmesolver.image.repository|acmesolver image repository|[![](https://quay.io/static/img/quay_favicon.png)Quay](http://quay.io/jetstack/cert-manager-acmesolver)|
|acmesolver.image.tag|acmesolver image tag|v1.12.2|
|acmesolver.image.pullPolicy|acmesolver image pull policy|IfNotPresent|
|startupapicheck.enabled|Toggles whether the startupapicheck Job should be installed|true|
|startupapicheck.securityContext|Security context for startupapicheck pod assignment|refer to Default Security Contexts|
|startupapicheck.containerSecurityContext|Security context to be set on startupapicheck component container|refer to Default Security Contexts|
|startupapicheck.timeout|Timeout for 'kubectl check api' command|1m|
|startupapicheck.backoffLimit|Job backoffLimit|4|
|startupapicheck.jobAnnotations|Optional additional annotations to add to the startupapicheck Job|{}|
|startupapicheck.podAnnotations|Optional additional annotations to add to the startupapicheck Pods|{}|
|startupapicheck.extraArgs|Optional additional arguments for startupapicheck|[]|
|startupapicheck.resources|CPU/memory resource requests/limits for the startupapicheck pod|{}|
|startupapicheck.nodeSelector|Node labels for startupapicheck pod assignment|{}|
|startupapicheck.affinity|Node affinity for startupapicheck pod assignment|{}|
|startupapicheck.tolerations|Node tolerations for startupapicheck pod assignment|[]|
|startupapicheck.podLabels|Optional additional labels to add to the startupapicheck Pods|{}|
|startupapicheck.image.repository|startupapicheck image repository|[![](https://quay.io/static/img/quay_favicon.png)Quay](http://quay.io/jetstack/cert-manager-ctl)|
|startupapicheck.image.tag|startupapicheck image tag|v1.12.2|
|startupapicheck.image.pullPolicy|startupapicheck image pull policy|IfNotPresent|
|startupapicheck.serviceAccount.create|If true, create a new service account for the startupapicheck component|true|
|[startupapicheck.serviceAccount.name](http://startupapicheck.serviceaccount.name/ "http://startupapicheck.serviceAccount.name")|Service account for the startupapicheck component to be used. If not set and startupapicheck.serviceAccount.create is true, a name is generated using the fullname template||
|startupapicheck.serviceAccount.annotations|Annotations to add to the service account for the startupapicheck component||
|startupapicheck.serviceAccount.automountServiceAccountToken|Automount API credentials for the startupapicheck Service Account|true|
|maxConcurrentChallenges|The maximum number of challenges that can be scheduled as 'processing' at once|60|


```bash
# 설치
helm install cert-manager ./cert-manager -f ./cert-manager/values.yaml 

# 설치 확인
k get po -n mgmt -o wide | grep cert
```