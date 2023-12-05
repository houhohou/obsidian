[[리눅스]]에서 [[메시지큐]]를 관리하는 오픈소스
[[Rabbitmq 설치]]
[[Rabbitmq]] 기본 구성
![](https://i.imgur.com/ohVYAzW.png)


1. producer(메시지 생성자)가 메시지를 생성합니다.
2. 메시지를 브로커(RabbitMQ)에 보냅니다.
3. 브로커(RabbitMQ)는 Exchange에서 메시지를 받아 Exchange의 타입에 따라 라우팅하여 메시지를 큐로 보냅니다.
4. 해당 큐를 Listen 하고 있는 Consumer(소비자)가 메시지를 가져갑니다.


Exchange 타입

- Direct Exchange
    - Message의 Routing Key와 정확히 일치하는 Binding된 Queue로 Routing
- Fanout Exchange
    - Binding된 모든 Queue에 Message를 Routing
- Topic Exchange
    - 특정 Routing Pattern이 일치하는 Queue로 Routing
- Headers Exchange
    - key-value로 정의된 Header 속성을 통한 Routing




















https://www.cloudamqp.com/blog/part1-rabbitmq-for-beginners-what-is-rabbitmq.html