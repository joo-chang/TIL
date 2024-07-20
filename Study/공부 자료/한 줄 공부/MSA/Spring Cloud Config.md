## Spring Cloud Config

- 분산 시스템에서 서버, 클라이언트 구성에 필요한 설정 정보(application.yml)를 외부 시스템에서 관리하기 위한 방법
- 하나의 중앙화된 저장소에서 구성요소 관리 가능
- 각 서비스를 다시 빌드하지 않고, 바로 적용 가능
- 애플리케이션 배포 파이프라인을 통해 DEV-UAT-PROD 각각 환경에 맞는 구성 정보를 사용 가능

## 장점

- 환경 설정 파일에 있는 다양한 정보는 데이터베이스를 변경할 수 있는 정보가 있을 수 있다.
- 적용시켰던 어떤 Value 값을 바꾸고자 할 때 원래 애플리케이션 내부에 포함되어 있으면 다시 빌드해야 하지만 외부에 있는 값을 변경하고 그걸 반영시킨다고 하면 다시 빌드하고 배포할 일이 없어진다.
- 그만큼 자유도 있고 동적인 애플리케이션 구성이 가능해 진다.

## Spring Cloud Config 구현 순서

 Spring cloud config server -> Spring cloud config client -> config 값의 암호화 -> config를 저장한 repository를 private로 돌리고 config server에서 ssh를 통해서 repository와 연결하기


## Spring Cloud Config Server 구축

### 프로젝트 생성

- Spring Initializer -> Spring cloud config -> config server 체크

### 의존성 추가

```java
implementation 'org.springframework.cloud:spring-cloud-config-server'
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

### application.yml 에 설정 추가

```sql
server:
  port: 8888
spring:
  application:
    name: config
  cloud:
    config:
      server:
        git:
          uri: https://github.com/project/config-service.git
          search-paths: config-file/**
          default-label: main
          username: 
          password: 
```

- config 서버는 기본으로 8888 사용
- uri : 설정 파일이 있는 git repo 주소
- default-label : git branch name, 설정 정보 저장소 repo 브랜치
- search-paths : 설정 파일들을 찾을 경로, 설정 정보 저장소에서 설정 정보 파일 위치
- username, password : https 를 사용 시 인증 수단
- 저장소 주소 ssh or https 로 설정 시 별도 인증 값을 적어야 함. (pirvateKey or password)

### Main Class에 `@EnableConfigServer` 추가

```java
@SpringBootApplication  
@EnableConfigServer  
public class ConfigServiceApplication {  
    public static void main(String[] args) {  
        SpringApplication.run(ConfigServiceApplication.class, args);  
    }  
}
```

### 서버 실행 및 확인

- Config Server의 endpoint로 정상 동작 확인
- 저장소에 존재하는 `notification-service-dev.yml` 파일을 읽으려면 `http://localhost:8888/notification-service/dev` 라고 요청을 보내면 된다.

---
## Spring Cloud Config Client 구축

### 의존성 추가

```
implementation 'org.springframework.cloud:spring-cloud-starter-config'  
implementation 'org.springframework.cloud:spring-cloud-starter-bootstrap'  
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

### 환경에 맞는 bootstrap.yml 생성

```
// bootstrap-local.yml

spring:  
  application:  
    name: article-service  
  cloud:  
    config:  
      uri: http://localhost:8888  //config 서버 연동
      profile: local
```

- 해당 설정 후 서버 실행 시 github에 있는 설정 파일을 불러와 실행된다.

---
## Config 파일 수정

- Config 파일 수정 후 실시간 적용 방법은 github에 수정된 파일을 반영 후 actuator 기능 사용
- 각 서비스에 아래 설정 정보를 저장한다.
- 이후 `http://localhost:8000/actuator/refresh` 경로로 POST 요청을 보내게되면 수정 사항이 있을 경우 새롭게 파일을 읽어와 반영된다.

```
management:  
  endpoints:  
    web:  
      exposure:  
        include: "refresh"
```

