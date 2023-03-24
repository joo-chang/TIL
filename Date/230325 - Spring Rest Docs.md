## Spring Rest Docs

#### build.gradle 설정

Spring Rest Docs 로 API 문서를 생성하기 위해 .adoc 문서 스니핏을 생성해주는 Asciidoctor가 필요하다.

```java
asciidoctorExtensions 'org.springframework.restdocs:spring-restdocs-asciidoctor'
```


#### Controller 테스트 코드 작성

- `@WebMvcTest` : @SrpingBootTest 어노테이션은 보통 controller 부터 DB 까지 테스트하는 통합 테스트에서 사용하며, 가벼운 controller 슬라이스 테스트를 할 때, 이 어노테이션을 사용할 수 있다.
- `@MockBean(JpaMet
- https://velog.io/@bagt/API-%EB%AC%B8%EC%84%9C%ED%99%94%EC%99%80-Spring-Rest-Docs
- 
- `