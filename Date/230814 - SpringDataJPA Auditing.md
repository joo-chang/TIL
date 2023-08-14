## Auditing

Audit 는 사전적으로 감사, 심사하다 의 의미를 가지고 있다. SpirngDataJPA에서는 Auditing 기능을 제공한다. 이를 사용해 엔티티가 생성되고, 변경되는 시점을 감지하여 생성, 수정 시간, 생성한 사람, 수정한 사람을 기록할 수 있다.

서비스 운영 시 데이터가 생성되고 수정한 일자를 기록하고 트래킹하는 것은 중요하다.


### @EnableJpaAuditing

```java
@SpringBootApplication
@EnableJpaAuditing
public class SampleApplication {

    public static void main(String[] args) {
        SpringApplication.run(SampleApplication.class, args);
    }
}
```

`@EnableJpaAuditing` 어노테이션을 사용하여, Auditing을 활성화한다. Application 클래스에 붙이거나, `@Configuration` 어노테이션이 사용된 클래스에 붙이면 된다.


### Entity 코드 작성

```java
@Getter
@NoArgsConstructor
@EntityListeners(AuditingEntityListener.class)
@Entity
public class Post {

    @Id
    @GeneratedValue
    private Long id;

    private String title;

    private String content;

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;

    public Post(final String title, final String content) {
        this.title = title;
        this.content = content;
    }
}
```


#### @EntityListensers

Auditing을 적용할 엔티티 클래스에 `@EntityListeners` 애노테이션을 적용해야한다. 해당 애노테이션은 엔티티 변화를 감지하여 엔티티와 매핑된 테이블의 데이터를 조작한다.

`@EntityListeners(AuditingEntityListener.class)` AuditingEntityListener 클래스는 SpringDataJPA에서 제공하는 이벤트 리스너로 엔티티의 영속, 수정 이벤트를 감지하는 역할을 한다.

#### @CrateData

생성일을 기록하기 위해 LocalDataTime 타입의 필드에 애노테이션을 적용한다. 또한 생성 일자는 수정이 불가능 해야하니 `@Column(updateable=false)` 를 적용한다. 이렇게 설정하면 엔티티 생성 감지 시점을 `createAt` 필드에 저장한다.


#### @LastModifiedDate

수정일을 기록하기 위해 `@LastModifiedDate`를 적용한다.   
수정 시점을 `updateAt` 필드에 저장한다.


---

### BaseEntity로 분리

생성 시간과 수정 시간은 대부분 엔티티에 사용된다. 따라서 이를 분리하여 다른 엔티티에서 상속하여 사용해 중복 코드를 방지한다.


```java
@Getter
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
public class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;

    @LastModifiedDate
    private LocalDateTime updatedAt;
}
```

`@MappedSuperclass` 애노테이션은 공통 매핑 정보가 필요할 때 부모 클래스에 선언된 필드를 상속받는 클래스에서 그대로 사용할 때 사용한다. 이때, 부모 클래스에 대한 테이블은 별도로 생성되지 않는다.

```java
@Getter
@NoArgsConstructor
@Entity
public class Post extends BaseEntity {

    @Id
    @GeneratedValue
    private Long id;

    private String title;

    private String content;

    public Post(final String title, final String content) {
        this.title = title;
        this.content = content;
    }
}
```

`@EntityListeners` 애노테이션과, `createdAt`, `updatedAt` 필드를 제거한 후 `BaseEntity` 를 상속받는다.