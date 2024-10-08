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

localhost:12011/user-service/auth ^pRzmDVVF

localhost:12011/user-service/auth ^mndibxZm

gateway ^OzNsunGU

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