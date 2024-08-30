# RabbitMQ Docker-compose

### Environment
![Static Badge](https://img.shields.io/badge/docker-24.0.6-%232496ed?style=flat&logo=docker&link=https%3A%2F%2Fwww.rabbitmq.com%)
![Static Badge](https://img.shields.io/badge/rabbitmq-3.alpine-%23ff6600?style=flat&logo=rabbitmq&link=https%3A%2F%2Fwww.rabbitmq.com)

---

### Configure
RabbitMQ 기본 설정파일 입니다.
`rabbitmq.conf.example` 샘플 설정파일을 참고하여 작성합니다.

path - `.docker/rabbitmq/etc/rabbitmq.conf`

- `default_user`
  - management 로그인 계정
- `default_pass`
  - management 로그인 계정의 패스워드
- `stomp.default_user`
  - STOMP 인증 계정
- `stomp.default_pass`
  - STOMP 인증 계정의 패스워드
- `amqp1_0.default_user`
  - AMQP 인증 계정

---

### Plugins
RabbitMQ 플러그인을 활성화 합니다.

path - `.docker/rabbitmq/etc/enabled_plugins`

- [`rabbitmq_management`](https://www.rabbitmq.com/docs/management)
**RabbitMQ**의 노드와 클러스터를 관리하고 모니터링하기 위한 웹 기반 UI와 CLI 도구를 제공합니다.
HTTP 기반 API 형태로 관리됩니다.
- [`rabbitmq_web_stomp`](https://www.rabbitmq.com/docs/web-stomp)
WebSocket 연결을 통해 STOMP 프로토콜을 웹 애플리케이션에서 사용할 수 있도록 지원합니다.
대체 플러그인으로 WebMQTT 플러그인이 있습니다.

---
### Environment Variable
docker-compose.yml 내 사용할 환경변수를 설정합니다.

Sample - `.env.example`

path - `.env`

- `CONTAINER_NAME`
  - RabbitMQ Docker 컨테이너 명을 설정
- `EPMD_PORT`=4369
  - RabbitMQ에서 노드를 찾는 helper(discovery daemon)의 포트
- `AMQP_PORT`=5672
  - AMQP 포트
- `MANAGEMENT_PORT`=15672
  - management 포트
- `STOMP_PORT`=15674
  - STOMP 포트
- `ED_PORT`=25672
  - RabbitMQ에서 노드 간 통신을 위한 Erlang Distribution 서버 포트
- `RABBITMQ_NODENAME`=rabbit@hostname
  - RabbitMQ Node 명을 설정
- `RABBITMQ_USE_LONGNAME`=true
  - RabbitMQ Node에 Longname 활성화 여부 설정
- `RABBITMQ_ERLANG_COOKIE`=rabbitmq
  - RabbitMQ 클러스터 노드 간 공유할 쿠키파일의 키 설정

---
### Cluster Peering
RabbitMQ built-in 기능인 DNS Peer Discovery 방식으로 구현합니다.

**1. `rabbitmq.conf` 내 cluster formation을 설정합니다.**
- `cluster_formation.peer_discovery_backend = rabbit_peer_discovery_dns`
  - discovery 방식을 설정합니다.
- `cluster_formation.dns.hostname = {hostname}`
  - DNS hostname을 작성합니다.

**2. Docker 컨테이너 bash 접속**
- `docker exec -it {container} bash`  

**3. seed node로 cluster join**
- `rabbitmqctl join_cluster {node}`  
- *<u>클러스터에서 제외하기</u>*
  - 현재 node를 제외할 경우
    - *(실행 중인 경우)* `rabbitmqctl stop_app`
    - `rabbitmqctl reset`
    - `rabbitmqctl start_app`
  - 다른 node를 제외할 경우
    - *(실행 중인 경우)* `rabbitmqctl -n {other_node} stop_app`
    - `rabbitmqctl forget_cluster_node {other_node}`
  

