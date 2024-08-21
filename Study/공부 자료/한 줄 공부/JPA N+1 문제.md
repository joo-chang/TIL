- JPA N+1 문제는 데이터베이스 성능 저하를 유발하는 대표적인 문제 중 하나이다.
- 주로 `OneToMany` 나 `ManyToMany` 같은 다대일, 일대다 관계를 조회할 때 발생

<br>

## 상황

예를들어, Customer 와 Order가 일대다 관계를 가지고 있다고 가정

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "customer", fetch = FetchType.LAZY)
    private List<Order> orders = new ArrayList<>();
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String product;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_id")
    private Customer customer;
}
```

<br>

## 문제 발생 예시

- 고객과 관련된 모든 주문을 조회한다고 가정
- 고객 리스트를 조회할 때, 각 고객의 주문도 함께 조회하려고 할 때 문제가 발생할 수 있다.

```java
public interface CustomerRepository extends JpaRepository<Customer, Long> {
}

@Service
@RequiredArgsConstructor
public class CustomerService {
    private final CustomerRepository customerRepository;

    public List<Customer> getAllCustomersWithOrders() {
        List<Customer> customers = customerRepository.findAll(); // 1. 모든 고객을 조회
        for (Customer customer : customers) {
            System.out.println(customer.getOrders().size()); // 2. 각 고객의 주문 수를 출력
        }
        return customers;
    }
}
```

### N+1 문제 발생

- 위 코드에서 `customerRepository.findAll();` 은 모든 고객을 조회하는 SQL 쿼리를 한 번 실행

```sql
SELECT * FROM customer;
```

- 하지만 `customer.getOrder()` 를 호출할 때마다 해당 고객의 주문을 조회하는 추가적인 SQL 쿼리가 실행

```sql
SELECT * FROM orders WHERE customer_id = ?;
```

만약 DB에 10명의 고객이 있다면, 총 11개의 SQL 쿼리 실행 (고객 조회 1번 + 주문 조회 10번)
고객 수가 증가할수록 쿼리 수도 증가하게 되어, 성능에 큰 영향을 미친다.

<br>

## N+1 문제 해결 방법

### Fetch Join 사용

- QueryDSL 을 사용하여 `fetchJoin()` 을 활용하면 고객과 주믄을 한 번의 쿼리로 가져올 수 있다.


```java
import com.querydsl.jpa.impl.JPAQueryFactory;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public class CustomerRepositoryImpl implements CustomerRepositoryCustom {

    private final JPAQueryFactory queryFactory;

    public CustomerRepositoryImpl(JPAQueryFactory queryFactory) {
        this.queryFactory = queryFactory;
    }

    @Override
    public List<Customer> findAllWithOrders() {
        QCustomer customer = QCustomer.customer;
        QOrder order = QOrder.order;

        return queryFactory
            .selectFrom(customer)
            .leftJoin(customer.orders, order).fetchJoin() // 페치 조인
            .fetch();
    }
}
```


### Batch Size 설정

대량의 연관 데이터를 처리할 때, `@BatchSize` 를 설정하여, 지정된 크기만큼의 데이터를 한 번에 가져오도록 설정

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "customer", fetch = FetchType.LAZY)
    @BatchSize(size = 10)
    private List<Order> orders = new ArrayList<>();

    // Getters and Setters
}
```


### @EntityGraph 사용

`@EntityGraph` 를 사용하면 특정 쿼리에서 관련 엔티티를 즉시 로딩 (Eager Loading)으로 가져올 수 있다.

```java
public interface CustomerRepository extends JpaRepository<Customer, Long>, QuerydslPredicateExecutor<Customer> {
    @EntityGraph(attributePaths = {"orders"})
    List<Customer> findAll(Predicate predicate);
}
```

<br>

## 결론

Spring Data JPA에서 N+1 문제는 연관된 엔티티 조회 시 발생할 수 있는 일반적인 문제이며, 성능 저하가 발생할 수 있다.

이를 해결하기 위해 `FETCH JOIN` , `@EntityGraph` , `@BatchSize` 등을 적절히 사용해 쿼리 수를 줄이고, 성능을 최적화하는 것이 중요하다.

미리 N+1 문제를 인지하고 해결하는 것이 애플리케이션 성능을 유지하는 데 큰 도움이 된다.