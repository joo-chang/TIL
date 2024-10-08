## Kafka란?

- 카프카는 분산 이벤트 스트리밍 플랫폼이다. 대용량 데이터 스트리밍을 처리하고 저장할 수 있는 오픈 소스 솔루션이다. 주로 이벤트 스트리밍, 데이터 파이프라인 및 실시간 데이터 피드를 구축하는데 사용한다.
- 메시지 큐와 유사하지만, 대용량 데이터 스트림을 저장하고 실시간으로 분석하거나 처리하는 데 중점을 둔다.

<br>

## 역할

- 실시간 데이터 처리 : 대용량 데이터를 실시간 처리, 분석
- 데이터 통합 : 다양한 소스에서 데이터를 수집하고 이를 통합하여 분석
- 내결함성 : 데이터 손실 없이 안정적으로 데이터를 저장, 전송

<br>

## 장단점

### 장점

- 신뢰성
	- 데이터 복제 : 데이터를 여러 브로커에 복제하여 저장하므로, 단일 브로커 장애 시에도 데이터 손실 방지
	- 확인 매커니즘 : 데이터가 소비자에게 성공적으로 전달되었는지 확인하는 기능 제공
- 유연성
	- 다양한 소비자 패턴 : 여러 소비자가 동시에 데이터를 구독할 수 있음
	- 프로토콜 지원 : 기본적으로 Kafka 프로토콜을 사용하지만, 다양한 클라이언트를 통해 다른 언어에서도 사용 가능
- 확장성
	- 분산 시스템 : 클러스터링을 통해 여러 노드에서 데이터 분산 처리
	- 수평 확장 : 브로커와 파티션을 추가하여 쉽게 확장
- 성능
	- 높은 처리량 : 대용량 데이터를 실시간으로 빠르게 처리
	- 저지연 : 데이터 전송 지연을 최소화하여 실시간 처리
- 관리 및 모니터링
	- 관리 도구 : 다양한 관리 도구를 통해 클러스터를 모니터링하고 관리
	- 플러그인 시스템 : 다양한 플러그인을 통해 기능 확장

### 단점

- 설정 및 운영 복잡성
	- 복잡한 설정 : 초기 설정이 다소 복잡할 수 있으며, 클러스터링 및 분산 환경에서는 더 많은 설정이 필요
	- 운영 관리 : 대규모 환경에서 운영, 관리하는 데 추가적인 노력이 필요
-  성능 문제
	- 브로커 오버헤드 : 높은 트래픽 상황에서는 브로커의 오버헤드가 발생할 수 있음
	- 대규모 메시지 처리 : 매우 대규모의 메시지를 처리할 때 성능 저하가 발생할 수 있으며, 적절한 클러스터링 및 최적화가 필요
- 운영 비용
	- 리소스 소비 : 메모리와 CPU 자원을 많이 소비할 수 있어, 충분한 리소스를 제공해야 함
	- 모니터링 및 유지보수 : 지속적인 모니터링과 유지보수가 필요하며, 추가적인 인력과 비용이 발생할 수 있음
- 러닝 커브
	- 학습 필요성 : 개념과 설정을 이해하는 데 시간이 걸릴 수 있음



![[Pasted image 20240411205640.png]]

![[Pasted image 20240411205659.png]]

데이터가 어떤 시스템에 저장되는지 관계하지 않고 무조건 Kafka만 상대하면 된다.
카프카에 쌓인 데이터 메세지들도, 서비스들에 데이터를 전달해줄 때 카프카에서 받아오면 되기 때문에 단일 포맷을 사용할 수 있다.





## 기본 구성 요소

### Message

- 메시지는 Kafka를 통해 전달되는 데이터 단위
- 메시지는 key, value, timestamp, 몇 가지 메타데이터로 구성

### Kafka Cluster

- 메세지를 저장하는 저장소이다.
- 하나의 카프카 클러스터는 여러 개 브로커(서버로 보면 됨)로 구성

### Producer

- 카프카 클러스터에 메세지를 보내는 역할
- 프로듀서는 특정 토픽에 메시지를 보냄

### Topic

- 메시지를 저장하는 장소
- 메시지는 토픽에 저장되었다가 소비자에게 전달
- 토픽은 여러 파티션으로 나누어질 수 있으며, 파티션은 메시지를 순서대로 저장
- 파티션을 통해 병렬 처리가 가능

### Partition

- 파티션은 토픽을 물리적으로 나눈 단위
- 각 파티션은 독립적으로 메시지를 저장하고 관리
- 파티션을 통해 데이터를 병렬로 처리할 수 있으며, 클러스터 내의 여러 브로커에 분산시켜 저장 가능

### Key 

- 키는 메시지를 특정 파티션에 할당하는 데 사용되는 값
- 동일한 키를 가진 메시지는 항상 동일한 파티션에 저장

### Consumer

- 토픽에서 메시지를 가져와 처리하는 역할
- 컨슈머는 특정 컨슈머 그룹에 속하며, 같은 그룹에 속한 컨슈머들은 토픽의 파티션을 분산 처리
- 기본적으로 컨슈머는 스티키 파티셔닝을 사용한다. 특정 컨슈머가 특정 파티션에 붙어서 계속해서 데이터를 처리하는 방식으로, 데이터 지역성을 높여 캐시 히트율을 증가시키고 전반적인 처리 성능을 향상

### Broker

- Kafka 클러스터의 각 서버를 의미. 메시지를 저장하고 전송하는 역할
- 하나의 Kafka 클러스터는 여러 브로커로 구성될 수 있으며, 각 브로커는 하나 이상의 토픽 파티션을 관리
### Zookeeper

- 카프카 클러스터를 관리, 조정하는 데 사용되는 분산 코디네이션 서비스
- 브로커의 메타데이터를 저장하고, 브로커 간 상호작용을 조정

![[Pasted image 20240109184358.png]]

<br>

## 토픽과 파티션

- 토픽은 메세지를 구분하는 단위 (파일 시스템의 폴더와 유사함)
- 한 개의 토픽은 한 개 이상의 파티션으로 구성
	- 파티션은 메세지를 저장하는 물리적인 파일

프로듀서는 메세지를 저장할 때 어떤 토픽에 저장
컨슈머는 어떤 토픽에서 메세지를 읽어옴

![[Pasted image 20240109184417.png]]

<br>
