## 프로젝트 소개

MSA를 기반으로 한 고성능 티켓팅 플랫폼을 개발합니다. 공연, 콘서트 등의 티켓을 구매할 수 있는 온라인 플랫폼으로, 사용자에게 빠르고 안정적인 티켓팅 서비스를 제공하는 것을 목표로 합니다.

## 프로젝트 목표

- **고성능 티켓팅 시스템 구축:** 특정 시간에 몰리는 대규모 트래픽을 안정적으로 처리할 수 있는 티켓팅 시스템을 구현하고, 이를 통해 티켓 예약과 결제가 원활하게 이루어지도록 합니다.
- **성능 개선 및 모니터링:** 새로운 기술 도입이나 성능 개선 후 MVP 단계마다 성능 체크를 실시하여 시스템 성능을 지속적으로 최적화하고, Grafana + Prometheus 기반 모니터링 시스템으로 사용자 요청과 서버 상태를 실시간으로 파악합니다.
- **부하 분산 및 대기열 관리:** 오토 스케일링을 적용하여, 특정 시간에 트래픽이 몰리는 상황에서도 안정적인 서비스 제공을 보장합니다. Kafka를 이용해 대기열 시스템으로 사용자의 티켓팅 참여 순서를 관리하고 좌석 선택 시 다른 사용자가 임시 선택하지 못하게 하는 기능을 제공합니다.
- **MSA 기반 확장성:** MSA를 기반으로 사용자, 공연, 주문 등의 기능을 각각 독립적인 서비스로 분리하여 관리하고, 이를 통해 서비스 확장성과 유지 보수성을 높입니다.
- **결제 및 환불 관리:** 결제 시스템을 연동하여 편리한 결제 기능을 제공하고, 관리자 페이지에서 실시간으로 결제 내역과 환불 상태를 확인 및 관리할 수 있습니다.
- **보안 및 사용자 관리:** 회원 관리 기능을 통해 사용자 로그인/로그아웃, 실시간 사용자 요청 로그 확인, 민감 정보 암호화 등 보안성을 강화한 관리 시스템을 제공합니다.
- **배치 시스템을 통해 정산 :** Spring Batch를 이용하여 매일 정산 금액을 계산하여 판매자에게 정산해 줍니다.
- **부하 테스트** : 각 MVP 단계마다 JMeter를 사용해 부하 테스트를 실시하고, 그 결과를 기록하여 성능을 지속적으로 개선합니다.
- **CI/CD 구성** : Docker와 Jenkins를 활용해 CI/CD 파이프라인을 구축하고, 자동화된 배포 및 테스트 프로세스를 통해 안정적인 서비스 운영을 지원합니다.

## 인프라 설계도
![[Pasted image 20240930141825.png]]
## 시스템 구조도

![[Pasted image 20240930102658.png]]

## API 명세서

## 테이블 명세서
### 유저(`user`)

| 필드 이름         | 데이터 타입       | 설명                                               |
| ------------- | ------------ | ------------------------------------------------ |
| user_id       | LONG         | 사용자 ID, PK                                       |
| nickname      | VARCHAR(100) | 사용자 닉네임, UK                                      |
| email         | VARCHAR(255) | 사용자 이메일 ( [abc@abc.com](mailto:abc@abc.com)), UK |
| phone         | VARCHAR(100) | 사용자 연락처 (010-1234-1234)                          |
| password      | VARCHAR(255) | 사용자 비밀번호 (암호화)                                   |
| role          | VARCHAR(10)  | 사용자 역할 (`USER`, `SELLER`, `MANAGER`)             |
| profile_image | VARCHAR(255) | 프로필 사진(S3 URL)                                   |

### 공연(`concert`)

|필드 이름|데이터 타입|설명|
|---|---|---|
|concert_id|LONG|공연 ID, PK|
|stage_id|LONG|공연장 ID, FK|
|user_id|LONG|판매자 ID|
|concert_name|VARCHAR(255)|공연 제목|
|runtime|INTEGER|상영 시간 ex)100|
|start_date|DATE|공연 시작일 (yyyy/MM/dd)|
|end_date|DATE|공연 종료일 (yyyy/MM/dd)|
|price|LONG|표 가격|
|concert_image|VARCHAR(255)|포스터 이미지(S3 URL)|

### 공연장(`stage`)

| 필드 이름    | 데이터 타입       | 설명         |
| -------- | ------------ | ---------- |
| stage_id | LONG         | 공연장 ID, PK |
| name     | VARCHAR(100) | 공연장 이름     |
| location | VARCHAR(255) | 공연장 위치     |
| row      | INTEGER      | 행 크기       |
| col      | INTEGER      | 열 크기       |

### 스케줄 (`schedule`)

| 필드 이름        | 데이터 타입 | 설명                 |
| ------------ | ------ | ------------------ |
| schedule_id  | LONG   | 공연 시간 ID, PK       |
| concert_id   | LONG   | 공연 ID, FK          |
| stage_id     | LONG   | 공연장 ID, FK         |
| concert_date | DATE   | 공연 날짜 (yyyy/MM/dd) |
| concert_time | DATE   | 공연 시간 (HH:mm:ss)   |

### 예매(`booking`)

|필드 이름|데이터 타입|설명|
|---|---|---|
|booking_id|LONG|예매 ID, PK|
|payment_id|LONG|결제 ID, FK|
|concert_id|LONG|공연 ID|
|user_id|LONG|사용자 ID|
|price|LONG|공연의 가격|
|status|VARCHAR(100)|예매 상태(|
|`PENDING`좌석만 선택한 상태,|||
|`CONFIRMED`결제까지 완료한 상태,|||
|`CANCELLED`취소된 상태)|||
|seat|VARCHAR(20)|좌석(행 열) ex) 3 3|

### 결제(`payment`)

|필드 이름|데이터 타입|설명|
|---|---|---|
|payment_id|LONG|결제 ID|
|user_id|LONG|사용자 ID|
|concert_id|LONG|공연 ID|
|total_price|LONG|금액|
|status|VARCHAR(100)|결제 상태 (|
|`WAITING` 결제 대기,|||
|`COMPLETED` 결제 완료,|||
|`FAILED` 결제 실패,|||
|`CANCELED` 결제 취소, `PART_CANCELED` 부분 취소)|||
|card|VARCHAR(100)|카드 정보 (암호화)|
|refund_price|LONG|환불 금액|

### 공통 필드(`BaseEntity`)

| 필드이름       | 데이터 타입       | 설명              |
| ---------- | ------------ | --------------- |
| is_deleted | BOOLEAN      | 삭제 여부           |
| created_at | TIMESTAMP    | 레코드 생성 시간       |
| created_by | VARCHAR(100) | 레코드 생성자 (email) |
| updated_at | TIMESTAMP    | 레코드 수정 시간       |
| updated_by | VARCHAR(100) | 레코드 수정자 (email) |
| deleted_at | TIMESTAMP    | 레코드 삭제 시간       |
| deleted_by | VARCHAR(100) | 레코드 삭제자 (email) |
## ERD
![[Pasted image 20240930141859.png]]