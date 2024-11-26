### 서비스 디스커버리란?

- MSA에서 각 서비스의 위치를 동적으로 관리하고 찾아주는 기능
- 각 서비스는 등록 서버(Eureka)에 자신의 위치를 등록하고, 이를 조회하여 통신
- 서비스 등록, 서비스 조회, 헬스 체크 등의 기능

<br>

### Eureka란?

- 넷플릭스가 개발한 서비스 디스커버리 서버로, MSA에서 각 서비스의 위치를 동적으로 관리
- 모든 서비스 인스턴스의 위치를 저장하는 중앙 저장소 역할, 서비스의 상태를 주기적으로 확인하여 가용성 보장
- 여러 인스턴스를 지원하여 고가용성을 유지할 수 있음

#### Eureka 서버 설정

- Eureka 서버는 서비스 레지스트리를 구성하는 중앙 서버

```yml
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false  # 다른 Eureka 서버에 이 서버를 등록하지 않음
    fetch-registry: false  # 다른 Eureka 서버의 레지스트리를 가져오지 않음
  server:
    enable-self-preservation: false  # 자기 보호 모드 비활성화
```

- 위 설정을 통해 Eureka 서버 구성, 클라이언트가 등록할 수 있도록 준비

#### Eureka 클라이언트 설정

- 각 서비스는 Eureka 서버에 자신을 등록해야 함

```yml
spring:
  application:
    name: my-service

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/  # Eureka 서버 URL
    register-with-eureka: true  # Eureka 서버에 등록
    fetch-registry: true  # Eureka 서버로부터 레지스트리 정보 가져오기
  instance:
    hostname: localhost  # 클라이언트 호스트 이름
    prefer-ip-address: true  # IP 주소 사용 선호
    lease-renewal-interval-in-seconds: 30  # 리스 갱신 간격
    lease-expiration-duration-in-seconds: 90  # 리스 만료 기간
```
