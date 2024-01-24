## REST

- REST(Representational State Transfer) 는 자원을 이름으로 구분하여 해당 자원의 상태를 주고 받는 모든 것을 의미한다.

> HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고,
> HTTP Method(POST, GET, PUT, DELETE, PATCH 등)를 통해 
> 해당 자원(URI)에 대한 CRUD를 적용하는 것을 의미합니다.

---

## REST API

REST API는 REST의 원리를 따르는 API를 의미한다.

> API : 데이터와 기능의 집합을 제공하여 컴퓨터 프로그램 간 상호작용을 촉진하며, 정보 교환을 가능하도록 하는 것

REST API를 올바르게 설계하기 위해 지켜야할 몇 가지 규칙

1. URI는 동사보다 명사, 대문자보다 소문자를 사용하여야 한다.
2. 마지막에 `/` 를 포함하지 않는다.
3. 언더바 대신 하이픈을 사용한다.
4. 파일 확장자는 URI에 포함하지 않는다.
5. 행위를 포함하지 않는다.

---
## RESTful

RESTful은 REST의 원칙을 엄격하게 준수하는 웹 서비스나 웹 애플리케이션을 의미한다.

REST API의 설계 규칙을 올바르게 지킨 시스템을 RESTful 하다고 말할 수 있다.
모든 CRUD 기능을 POST로 처리하거나 URI 규칙이 올바르지 않은 API는 REST API를 사용하였지만 RESTful 하지 못한 시스템이라고 할 수 있다.

---

요약하면, 
- REST는 웹 서비스를 위한 아키텍처 스타일로, 리소스를 URI로 표현하고 HTTP 메서드를 사용하여 상태를 전송하는 방식이고, 
- REST API는 REST를 기반으로 서비스 API를 구현한 것을 말하고,
- RESTful 서비스는 REST 원칙을 엄격하게 준수하는 웹 서비스나 웹 애플리케이션을 나타낸다.


