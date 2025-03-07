## *"Spring Framework를 왜 사용하나요? 무슨 장점이 있나요?"*

> 스프링은 자바의 오픈소스 애플리케이션 프레임워크 중 하나로 스프링의 기본 철학은 특정 기술에 종속되지 않고, 객체를 관리할 수 있는 프레임워크를 제공하는 것입니다. 
> 동적인 웹 사이트를 개발하기 위한 여러가지 서비스를 제공하고 있다.
> 컨테이너로 자바 객체를 관리하면서 의존성 주입과 제어의 역전을 통해 결합도를 낮추게 됩니다.

- POJO 기반의 구성으로 자바 코드를 이용해서 객체를 구성하는 방식 그대로 스프링에서 사용할 수 있다.    
	- 덕분에 높은 생산성과 유연한 테스트를 할 수 있다.
- DI(의존성 주입)을 통한 객체 관계 구성을 지원한다. 
- AOP(횡단 관심사 분리) 지원

## *"결합도를 낮다는 게 뭔가요?"*

> 결합도가 낮다는 것은 모듈 간의 독립성과 분리도가 높다는 의미입니다. 결합도가 낮을 때 장점은 모듈화 및 유지보수 용이성, 재사용성, 확장성, 시스템 안정성 향상이 있습니다.

---
## *"IoC와 DI에 대해 설명해 주세요."*

> IoC는 제어의 역전이라는 뜻으로, 빈 객체들의 생명주기를 스프링 컨테이너에게 위임하는 형식입니다. 규모가 큰 프로젝트에서는 객체를 하나하나 생성하고 의존관계를 맺어주는 코드를 직접 작성하는 일이 쉽지 않은데, IoC는 객체 생성/소멸/사용 등을 책임지기 때문에 유지 보수, 확장에 유리합니다.
> DI는 의존성 주입을 의미합니다. 객체간의 의존 관계를 미리 선언해 두면 스프링 컨테이너가 의존 관계를 자동으로 연결해줍니다.
> 이렇게 되면 직접 객체를 생성하거나 검색해서 가져올 필요없어서 결합도가 낮아지는 장점이 있습니다.

---
## *"DI의 개념과 DI가 어떻게 코드의 유연성과 테스트 용이성에 기여하는지 예를 들어 설명하시오"*

> DI는 객체가 필요로 하는 의존성을 외부에서 주입하는 디자인 패턴입니다. 이를 통해 객체는 필요한 의존성을 직접 생성하지 않고, 외부(Spring 컨테이너)에서 받게 됩니다.
> 예를 들어, 어떤 클래스가 데이터베이스 연결이 필요할 때, Spring은 이 클래스에 데이터베이스 연결 객체를 주입해 줍니다. 이 방식은 코드의 결합도를 낮추고, 유연성을 높입니다.
> 또한, 단위 테스트를 수행할 때 실제 의존성 대신 Mock 객체를 주입할 수 있어 테스트에 용이해집니다.

---
## *"JPA를 사용하는 이점은 무엇이며, 실제 프로젝트에서 어떻게 활용했는지 예를 들어 설명하시오"*

> JPA의 가장 큰 이점은 개발자가 객체 중심적으로 데이터베이스 작업을 할 수 있도록 해주는 것입니다. SQL을 직접 작성하지 않고 객체와 그 관계를 이용하여 데이터를 관리할 수 있습니다.
> 이는 개발 시간을 단축시키고, 코드의 가독성과 유지 보수성을 향상시킵니다.

> 관리자페이지 개발 시에 JPA를 사용하여 거래내역을 관리했습니다. 엔티티들의 관계를 객체로 모델링하여, 복잡한 조인 쿼리 없이도 필요한 데이터를 쉽게 조회하고 관리할 수 있었습니다. 또한, JPA의 캐싱 기능을 사용하여 데이터베이스에 대한 쿼리 수를 줄이고 성능을 개선할 수 있었습니다.

---
## *"Spring Security를 사용하여 어떻게 안전한 웹 애플리케이션을 구축할 수 있는지 설명해 주세요. 구체적인 기능이나 구현 방법에 대해서도 언급해 주시면 좋겠습니다."*

> 스프링 시큐리티는 웹 애플리케이션의 보안을 강화하기 위한 포괄적인 솔루션을 제공합니다. 예를 들어 URL 접근 제어를 통해 특정 권한을 가진 사용자만이 특정 경로에 접근할 수 있도록 설정할 수 있습니다. 또한, 메소드 수준의 보안을 통해 비즈니스 로직에 대한 접근을 세밀하게 제어할 수도 있습니다.
> 제가 참여한 프로젝트에서 Spring Security와 JWT 토큰을 사용하여 사용자 인증을 구현하였습니다. 로그인 시 사용자 인증 정보 검증 후 AccessToken, RefreshToken을 발급하고, 이후 사용자 요청마다 토큰을 검증하여 유효한 사용자인지 확인하였고, AccessToken 만료 시 RefreshToken을 이용하여 AccessToken을 재발급 받는 방식을 이용하여 보안을 강화했습니다.

- Refresh Token : 쿠키 
- Access Token : 로컬 스토리지

Refresh Token이 탈취 당하더라도 악의적인 HTTP 요청을 하는 CSRF 공격을  Refresh Token을 가지고 할 수 없기 때문에 안전하고, 
Access Token이 탈취 되더라도 토큰 만료 기간을 짧게 가져가 보안을 강화할 수 있다.

[[JWT 토큰 저장]]

---
## *"ORM에 대해 설명해 주세요."*

> ORM은 Object Relational Mapping의 약자로 관계형 데이터베이스와 객체지향 프로그래밍 언어 간의 데이터를 변환하고 매핑해주는 기술입니다. 
> ORM의 주요 목적은 SQL 쿼리를 직접 작성하지 않고도 데이터베이스 조작을 수행할 수 있도록 해주고. 데이터베이스에 독립적으로 동작할 수 있기 때문에 테이터베이스가 변경되더라도 애플리케이션 코드를 크게 수정하지 않아도 됩니다. 이외에도 객체 관리 및 성능 최적화가 있습니다.

---
## *"JPA란 무엇인가요?"*

> JPA는 자바에서 사용하는 ORM 기술 표준입니다. 자바 애플리케이션과 JDBC 사이에서 동작하며, 자바 인터페이스로 정의되어 있습니다.
> 자바 객체와 DB 테이블을 매핑 해줍니다. JPA 구현체로는 하이버네이트가 있습니다.

---
## *"QueyDSL은 왜 사용하나요?"*

> SpringDataJPA 같은 경우 복잡한 쿼리와 동적 쿼리를 구현하는 데 있어 한계가 있습니다. QueryDSL은 쿼리문을 문자열로 작성하는 mybatis, JPQL 등은 컴파일 시 오류 발견하는 것이 불가능한데, QueryDSL은 자바 코드로 SQL문을 작성할 수 있어 잘못 입력 시 컴파일 시 오류를 발생하므로 런타임 시 발생하는 치명적인 오류를 방지할 수 있습니다.

---
## *"스프링 컨테이너? 애플리케이션 컨텍스트?"*

> 빈의 생명주기를 관리하며, 생성된 빈들에게 추가적인 기능을 제공합니다.

## *"스프링 컨테이너를 사용하는 이유는?"*

> 객체를 생성하기 위해서 new 생성자를 사용합니다. 그로 인해 애플리케이션에는 수많은 객체가 존재하고 서로를 참조하게 됩니다. 객체 간의 참조가 많아지면 의존성이 높아지게 됩니다.
> 이는 낮은 결합도와 높은 응집도를 지향하는 객체지향 프로그래밍의 핵심과 멀기 때문에 객체 간의 의존성을 낮추어 결합도는 낮추고, 높은 캡슐화를 위해 스프링 컨테이너를 사용합니다.

