## Layered Architecture

계층화 아키텍처는 애플리케이션의 컴포넌트를 유사 관심사를 기준으로 레이어(계층)로 묶어 수평적으로 구성한 구조를 말한다.
일반적으로 프레젠테이션(Presentation) 계층, 비지니스(Business) 계층, 데이터 접근(Data Access) 계층의 3계층으로 구성된다.


### 프레젠테이션 계층 (Presentation Layer)

- 애플리케이션이 최상단 계층
- 클라이언트의 요청을 해석하고 응답
- UI 또는 API 를 제공
- 비지니스 로직은 포함하지 않고, 비지니스 계층으로 위임하여 반환받은 결과를 응답하는 역할만 수행한다.
- 스프링에서는 Controller에 해당한다.

### 비지니스 계층 (Business Layer)

- 애플리케이션이 제공하는 기능을 정의하고, 세부 작업을 수행한다.
- 스프링에서는 서비스 계층(Service Layer)

### 데이터 접근 계층 (Data Access Layer)

- 데이터베이스에 직접 접근하는 모든 작업을 수행한다.
- 스프링에서는 Repository에 해당한다.


---
## 스프링에서의 레이어드 아키텍처

스프링 부트는 일반적으로 spring-boot-starter-web 의존성을 사용할 때 Spring MVC 구조를 갖고 다음과 같은 레이어드 아키텍처를 이룬다.

![[Pasted image 20240307172332.png]]


Spring MVC는 Model - View - Controller 구조로, View와 Controller는 프레젠테이션 계층이고, Model은 비지니스 계층과 데이터 접근 계층 영역으로 구분할 수 있다.

DispatcherServlet이 프레젠테이션 계층을 담당한다. (직접적으로는 Controller를 통해 다른 계층과 통신)
비즈니스 계층에는 서비스를 배치하여 엔티티와 같은 도메인 객체와 함께 비즈니스 로직을 처리하고, 데이터 접근 계층에는 DAO(Data Access Object)를 배치하여 도메인을 관리한다.
Spring에서 대부분 사용하는 Spring Data JPA에서는 Repository가 DAO의 역할을 한다.

