## Spring Rest Docs

#### build.gradle 설정

Spring Rest Docs 로 API 문서를 생성하기 위해 .adoc 문서 스니핏을 생성해주는 Asciidoctor가 필요하다.

```java
asciidoctorExtensions 'org.springframework.restdocs:spring-restdocs-asciidoctor'
```


#### Controller 테스트 코드 작성

- `@WebMvcTest` : @SrpingBootTest 어노테이션은 보통 controller 부터 DB 까지 테스트하는 통합 테스트에서 사용하며, 가벼운 controller 슬라이스 테스트를 할 때, 이 어노테이션을 사용할 수 있다.

- `@MockBean(JpaMetamodelMappingContext.class)` 

> Spring 컨테이너를 요구하는 테스트느느 가장 기본이 되는 Application 클래스가 항상 로드되는데, @EnableJpaAuditing이 해당 클래스에 등록되어 있어 모든 테스트들이 항상 JPA 관련 Bean들을 필요로 하게 된다.

```
@EnableJpaAuding
@SpringBootApplication
```

> @SpringBootTest를 사용할 때는 전체 context를 로드하고 JPA를 포함한 모든 Bean을 주입받기 때문에 에러가 발생하지 않지만, @WebMvcTest 같은 슬라이드 테스트는 JPA 관련 Bean 들을 로드하지 않기 떄문에 에러가 발생한다.

따라서, 이 어노테이션을 사용해 Mock 객체로 JPA 관련 Bean 들을 주입해준 것이다.

