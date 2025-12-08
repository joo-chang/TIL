
## 📚 테이블 목록

### 1. 👥 회원 관리 (Member Management)
- [users](#1-users-사용자) - 사용자 정보
- [sellers](#2-sellers-판매자) - 판매자 정보  
- [seller_documents](#3-seller_documents-판매자-문서) - 판매자 제출 문서

### 2. 🏷️ 경매 시스템 (Auction System)
- [quotes](#4-quotes-견적-요청) - 견적 요청
- [bids](#5-bids-입찰-정보) - 입찰 정보
- [bid_history](#6-bid_history-입찰-수정-이력) - 입찰 수정 이력

### 3. 💰 거래 관리 (Transaction Management)
- [contracts](#7-contracts-계약) - 계약 정보
- [payments](#8-payments-결제-정보) - 결제 정보
- [deliveries](#9-deliveries-배송-정보) - 배송 정보

### 4. 🔔 지원 시스템 (Support System)
- [notifications](#10-notifications-사용자-알림) - 사용자 알림

---

## 📋 테이블 상세 명세

### 1. users (사용자)

**테이블 설명**: 플랫폼을 이용하는 모든 사용자(소비자, 판매자, 관리자)의 기본 정보를 저장하는 테이블

| 컬럼명         | 데이터 타입       | 제약조건        | 설명                               |
| ----------- | ------------ | ----------- | -------------------------------- |
| id          | UUID         | PRIMARY KEY | 사용자 고유 식별자                       |
| email       | VARCHAR(255) | NOT NULL    | 사용자 이메일 주소                       |
| name        | VARCHAR(255) | NOT NULL    | 사용자 이름 또는 닉네임                    |
| role        | VARCHAR(255) | NOT NULL    | 사용자 역할 (CONSUMER, SELLER, ADMIN) |
| provider    | VARCHAR(255) | NOT NULL    | 소셜 로그인 제공자 (KAKAO, NAVER)        |
| provider_id | VARCHAR(255) | NOT NULL    | 소셜 로그인 고유 ID                     |



---

### 2. sellers (판매자)

**테이블 설명**: 판매자로 등록된 사용자의 사업자 정보와 승인 상태를 관리하는 테이블

| 컬럼명             | 데이터 타입       | 제약조건                      | 설명                                  |
| --------------- | ------------ | ------------------------- | ----------------------------------- |
| user_id         | UUID         | PRIMARY KEY, FK(users.id) | 사용자 ID (1:1 관계)                     |
| business_number | VARCHAR(255) | NOT NULL                  | 사업자등록번호                             |
| store_name      | VARCHAR(255) | NOT NULL                  | 상호명/매장명                             |
| approval_status | VARCHAR(255) | NOT NULL                  | 승인 상태 (PENDING, APPROVED, REJECTED) |

---

### 3. seller_documents (판매자 문서)

**테이블 설명**: 판매자가 제출한 사업자등록증, 사전승낙서 등의 문서 정보를 저장하는 테이블

| 컬럼명 | 데이터 타입 | 제약조건 | 설명 |
|--------|-------------|----------|------|
| id | UUID | PRIMARY KEY | 문서 고유 식별자 |
| seller_id | UUID | FK(sellers.user_id) | 판매자 ID |
| type | VARCHAR(255) | | 문서 종류 (BUSINESS_LICENSE, CONSENT_FORM) |
| file_url | VARCHAR(255) | | S3 저장소 파일 URL |
| uploaded_at | TIMESTAMP | | 업로드 일시 |

---

### 4. quotes (견적 요청)

**테이블 설명**: 소비자가 등록한 휴대폰 구매 견적 요청 정보를 저장하는 테이블

| 컬럼명        | 데이터 타입       | 제약조건                   | 설명                               |
| ---------- | ------------ | ---------------------- | -------------------------------- |
| id         | UUID         | PRIMARY KEY            | 견적 고유 식별자                        |
| user_id    | UUID         | NOT NULL, FK(users.id) | 견적 요청자 ID                        |
| model      | VARCHAR(255) | NOT NULL               | 휴대폰 기종명 (예: iPhone 16)           |
| storage    | VARCHAR(255) | NOT NULL               | 저장 용량 (예: 128GB)                 |
| carrier    | VARCHAR(255) | NOT NULL               | 통신사 (SKT, KT, LGU+)              |
| color      | VARCHAR(255) | NOT NULL               | 색상                               |
| status     | VARCHAR(255) | NOT NULL               | 견적 상태 (OPEN, CLOSED, CONTRACTED) |
| expired_at | TIMESTAMP    | NOT NULL               | 경매 마감 시각                         |



---

### 5. bids (입찰 정보)

**테이블 설명**: 판매자가 견적에 대해 제출한 입찰 정보를 저장하는 테이블

| 컬럼명             | 데이터 타입           | 제약조건                          | 설명           |
| --------------- | ---------------- | ----------------------------- | ------------ |
| id              | UUID             | PRIMARY KEY                   | 입찰 고유 식별자    |
| quote_id        | UUID             | NOT NULL, FK(quotes.id)       | 대상 견적 ID     |
| seller_id       | UUID             | NOT NULL, FK(sellers.user_id) | 입찰자 ID       |
| price           | INTEGER          | NOT NULL                      | 입찰가 (원)      |
| delivery_days   | INTEGER          | NOT NULL                      | 배송 예상일 (일)   |
| rating_snapshot | DOUBLE PRECISION |                               | 입찰 당시 판매자 평점 |


---

### 6. bid_history (입찰 수정 이력)

**테이블 설명**: 입찰 정보의 수정 이력을 추적하기 위한 테이블

| 컬럼명           | 데이터 타입    | 제약조건                  | 설명          |
| ------------- | --------- | --------------------- | ----------- |
| id            | UUID      | PRIMARY KEY           | 이력 고유 식별자   |
| bid_id        | UUID      | NOT NULL, FK(bids.id) | 대상 입찰 ID    |
| version       | INTEGER   | NOT NULL              | 수정 버전 번호    |
| price         | INTEGER   | NOT NULL              | 수정 전 입찰가    |
| delivery_days | INTEGER   | NOT NULL              | 수정 전 배송 예상일 |

---

### 7. contracts (계약)

**테이블 설명**: 소비자가 입찰을 선택하여 성사된 계약 정보를 저장하는 테이블

| 컬럼명 | 데이터 타입 | 제약조건 | 설명 |
|--------|-------------|----------|------|
| id | UUID | PRIMARY KEY | 계약 고유 식별자 |
| quote_id | UUID | NOT NULL, FK(quotes.id) | 견적 ID |
| bid_id | UUID | NOT NULL, FK(bids.id) | 선택된 입찰 ID |
| status | VARCHAR(255) | NOT NULL | 계약 상태 (SIGNING, SIGNED, CANCELLED) |
| signed_at | TIMESTAMP | | 계약 체결 일시 |


---

### 8. payments (결제 정보)

**테이블 설명**: 계약에 따른 결제 정보와 PG사 연동 데이터를 저장하는 테이블

| 컬럼명 | 데이터 타입 | 제약조건 | 설명 |
|--------|-------------|----------|------|
| id | UUID | PRIMARY KEY | 결제 고유 식별자 |
| contract_id | UUID | NOT NULL, FK(contracts.id) | 계약 ID |
| amount | INTEGER | NOT NULL | 결제 금액 (원) |
| method | VARCHAR(255) | NOT NULL | 결제 수단 (CARD, BANK, MOBILE) |
| status | VARCHAR(255) | NOT NULL | 결제 상태 (REQUESTED, PENDING_APPROVAL, PAID, FAILED) |
| pg_tid | VARCHAR(255) | NOT NULL | PG사 거래 ID |
| paid_at | TIMESTAMP | | 결제 완료 일시 |


---

### 9. deliveries (배송 정보)

**테이블 설명**: 계약 상품의 배송 정보와 배송 상태를 추적하는 테이블

| 컬럼명 | 데이터 타입 | 제약조건 | 설명 |
|--------|-------------|----------|------|
| id | UUID | PRIMARY KEY | 배송 고유 식별자 |
| contract_id | UUID | NOT NULL, FK(contracts.id) | 계약 ID |
| courier | VARCHAR(255) | NOT NULL | 택배사 (CJ_LOGISTICS, HANJIN, LOTTE) |
| invoice_number | VARCHAR(255) | NOT NULL | 송장 번호 |
| status | VARCHAR(255) | NOT NULL | 배송 상태 (READY, SHIPPED, DELIVERED) |
| shipped_at | TIMESTAMP | | 발송 일시 |


---

### 10. notifications (사용자 알림)

**테이블 설명**: 사용자에게 발송되는 각종 알림 정보를 저장하는 테이블

| 컬럼명        | 데이터 타입       | 제약조건                   | 설명                                   |
| ---------- | ------------ | ---------------------- | ------------------------------------ |
| id         | UUID         | PRIMARY KEY            | 알림 고유 식별자                            |
| user_id    | UUID         | NOT NULL, FK(users.id) | 수신 대상 사용자 ID                         |
| type       | VARCHAR(255) | NOT NULL               | 알림 유형 (QUOTE_CREATED, BID_ARRIVED 등) |
| channel    | VARCHAR(255) | NOT NULL               | 발송 채널 (KAKAO, PUSH, EMAIL)           |
| is_read    | BOOLEAN      | NOT NULL               | 읽음 여부                                |

---

## 🔗 테이블 관계도

### 주요 관계
- **users** 1:1 **sellers** (사용자-판매자)
- **sellers** 1:N **seller_documents** (판매자-문서)
- **users** 1:N **quotes** (사용자-견적)
- **quotes** 1:N **bids** (견적-입찰)
- **bids** 1:N **bid_history** (입찰-이력)
- **quotes** 1:1 **contracts** (견적-계약)
- **bids** 1:1 **contracts** (입찰-계약)
- **contracts** 1:1 **payments** (계약-결제)
- **contracts** 1:1 **deliveries** (계약-배송)
- **users** 1:N **notifications** (사용자-알림)

