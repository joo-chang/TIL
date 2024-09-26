---

excalidraw-plugin: parsed
tags: [excalidraw]

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠== You can decompress Drawing data with the command palette: 'Decompress current Excalidraw file'. For more info check in plugin settings under 'Saving'


# Excalidraw Data
## Text Elements
프로젝트 설계 ^kCeXjyZI

com.fortickets.user
├── application
│   ├── service
│   │   └── OrderService.java
│   ├── dto
│   │   ├── request
│   │   │   └── GetOrderReq.java
│   │   └── response
│   │   │   └── GetOrderRes.java
│   ├── client
│   │   ├── OrderClient.java
├── domain
│   ├── entity
│   │   ├── Order.java
│   │   ├── Product.java
│   │   └── ValueObject.java
│   ├── repository
│   │   └── OrderRepository.java
├── infrastructure
│   ├── configuration
│   │   └── DatabaseConfig.java
│   └── messaging
│       └── KafkaMessageProducer.java
└── presentation
    └── controller
        └── OrderController.java ^ouX0wZ86

패키지 구조 ^dtzn9hjP

- 대기열
    - **Kafka Producer**: 사용자가 티켓 구매 요청을 하면, 이 요청을 Kafka 토픽에 전송하여 대기열에 쌓음.
    - **Kafka Consumer**: 대기열에서 요청을 하나씩 가져와 처리하고, 성공적으로 티켓을 구매한 사용자에게 결과 전송.
    - **Spring Cloud Gateway**: 요청을 마이크로서비스로 라우팅하고, 각 서비스가 독립적으로 작동할 수 있도록 관리. 
            - 사용자의 요청은 Kafka의 특정 토픽에 저장되고, 각 요청은 partition과 offset으로 관리.
            - `KafkaTemplate`을 사용하여 Kafka에 메시지를 전송하고, 메시지가 어느 파티션과 오프셋에 저장되었는지 확인.
            - Kafka 컨슈머를 통해 현재 처리된 offset과 대기 중인 offset을 추적하여 순차적으로 처리.
        2. **대기표 발급 및 요청 관리**:
            - 대기표가 없는 최초 요청자는 UUID를 생성하여 Kafka에 요청을 전송하고, 해당 요청이 어느 파티션에 할당되었는지와 offset을 저장.
            - 대기표를 이미 발급받은 사용자는 요청 헤더에 저장된 `KAFKA.UUID`, `KAFKA.PARTITION`, `KAFKA.OFFSET` 값을 사용하여 대기열 상태를 확인.
            - 요청을 처리할 순서가 되지 않으면 대기열에 다시 대기시킴.
        3. **대기 취소 처리**:
            - 사용자가 대기열에서 취소를 요청할 수 있으며, 취소 요청이 들어오면 priorityQueue에 해당 offset 값을 추가하고 응답을 반환.
            - 취소된 대기표는 우선순위 큐를 이용하여 대기열에서 제외.
        4. **대기열 상태 확인 및 응답 처리**:
            - 사용자의 요청이 처리될 순서가 되었는지 Kafka의 `committedOffset`과 `endOffset`을 통해 확인.
            - 대기열에서 앞선 요청이 모두 취소된 경우, 요청을 처리하고 Kafka 컨슈머에서 메시지를 commit.
            - 요청이 처리될 순서가 아니면 `JsonResponse` 형식으로 응답을 반환하고, 클라이언트는 요청이 대기 중임을 알림.
        5. **Kafka 컨슈머 설정**:
            - Kafka 컨슈머는 특정 파티션의 `committedOffset`과 `endOffset`을 가져와 현재 처리 중인 요청 상태를 확인.
            - 컨슈머는 자동으로 offset을 관리하지 않으며, 직접 commit을 통해 요청 처리 여부를 관리.
    - 요청 방식
        1. 최초 요청
            1. 일반 다른 요청들과 다를 바 없다. 만일 내 앞에 대기열이 존재하지 않는다면, 그 즉시 원하는 응답을 받을 수 있지만, 
                그렇지 않고 내 앞에 대기열이 존재할 경우 307 http status code와 함께 대기표 정보를 응답으로 받음. 대기표에는 아래 정보가 담긴다.
                - `JsonResponse`
                    - `uuid` : 대기표 고유 번호
                    - `offset` : kafka에 담긴 대기표 레코드의 offset 값 (나의 대기 순번)
                    - `partition` : 나의 대기표 레코드가 담긴 kafka의 partition 번호
                    - `committedOffset` : 제일 최근에 처리 완료된 레코드의 offset 값
                    - `endOffset` : 제일 최근에 요청이 들어온 레코드의 offset 값
        2. 재요청 (polling)
            1. 최초 요청에서 307 코드를 응답으로 받았다면, client는 원래 원했던 응답을 받을 때 까지 서버에게 재요청을 보낸다. 
                재요청을 보낼 때는 최초 요청에서 응답받았던 대기표를 요청 정보에 함께 담아서 전송해야 함. 
                만일 대기표를 요청 정보에 담지 않고 보낸다면, server에서는 해당 요청을 최초 요청으로 간주하고 대기순서를 맨 뒤로 보내버릴수도 있으니 주의. 
                계속해서 재요청을 보내면서 내 차례가 왔는지 확인하고, 내 차례가 왔다면 2xx 응답 코드와 함께 정상 응답을 받는다. 
                아직 내 차례가 오지 않았다면 최초 요청 때와 마찬가지로 307 응답 코드와 함께 대기표 정보를 갱신해서 받는다.
            - `내 앞에 쌓인 요청 수` = `offset` - `committedOffset` - `1`
            - `내 뒤에 쌓인 요청 수` = `endOffset` - `offset`
        3. 취소 요청
            1. client가 대기표를 끊어놓은 후, 정상 응답을 받기도 전에 대기줄에서 이탈할 때 보내는 요청. 
                대기표 취소 요청을 전송하면 server에서는 해당 대기표 순서가 왔을 때 건너뛰게 됨. kafka에서는 한 번 쌓인 레코드는 삭제 및 변경이 불가능하기 때문에 별도의 로직이 필요. ^IxOSxs8D

- **고성능 티켓팅 시스템 구축:** 특정 시간에 몰리는 대규모 트래픽을 안정적으로 처리할 수 있는 티켓팅 시스템을 구현하고, 이를 통해 티켓 예약과 결제가 원활하게 이루어지도록 합니다.
- **성능 개선 및 모니터링:** 새로운 기술 도입이나 성능 개선 후 MVP 단계마다 성능 체크를 실시하여 시스템 성능을 지속적으로 최적화하고, Grafana + Prometheus 기반 모니터링 시스템으로 사용자 요청과 서버 상태를 실시간으로 파악합니다.
- **부하 분산 및 대기열 관리:** 오토 스케일링을 적용하여, 특정 시간에 트래픽이 몰리는 상황에서도 안정적인 서비스 제공을 보장합니다. Kafka를 이용해 대기열 시스템으로 사용자의 티켓팅 참여 순서를 관리하고 좌석 선택 시 다른 사용자가 임시 선택하지 못하게 하는 기능을 제공합니다.
- **MSA 기반 확장성:** MSA를 기반으로 사용자, 공연, 주문 등의 기능을 각각 독립적인 서비스로 분리하여 관리하고, 이를 통해 서비스 확장성과 유지 보수성을 높입니다.
- **결제 및 환불 관리:** 결제 시스템을 연동하여 편리한 결제 기능을 제공하고, 관리자 페이지에서 실시간으로 결제 내역과 환불 상태를 확인 및 관리할 수 있습니다.
- **보안 및 사용자 관리:** 회원 관리 기능을 통해 사용자 로그인/로그아웃, 실시간 사용자 요청 로그 확인, 민감 정보 암호화 등 보안성을 강화한 관리 시스템을 제공합니다.
- **배치 시스템을 통해 정산 :** Spring Batch를 이용하여 매일 정산 금액을 계산하여 판매자에게 정산해 줍니다.
- **부하 테스트** : 각 MVP 단계마다 JMeter를 사용해 부하 테스트를 실시하고, 그 결과를 기록하여 성능을 지속적으로 개선합니다.
- **CI/CD 구성** : Docker와 Jenkins를 활용해 CI/CD 파이프라인을 구축하고, 자동화된 배포 및 테스트 프로세스를 통해 안정적인 서비스 운영을 지원합니다. ^sNQLP8K0

프로젝트 목표 ^EFIGXwOO

## Embedded Files
52dccb74692594b6486162f17767793676902041: [[Pasted Image 20240924235333_471.png]]
528989dc4002f941c911e9cf8755c114278cbfd7: [[Pasted Image 20240924235349_486.png]]
5f1c31c489fde2fb40359ade74bedb0cddb21e3e: [[Pasted Image 20240924235349_504.png]]

%%
## Drawing
```compressed-json
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
```
%%