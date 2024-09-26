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