## API에서 Entity를 직접 조회하면 안되는 이유

- 도메인이 API에 의존성이 생긴다. 나중에 변경, 유지보수가 어렵다.
- 캡슐화가 깨진다. 값을 어디서 꺼냈는지, 어디서 변경되었는지 알기 어렵다.
- JPA 연관 관계 때문에 N+1 문제가 발생하기 쉽다.

### 해결책 - Laze Loading, Fetch Join

1. 연관 관계는 Lazy를 기본으로 사용한다.
	- Lazy는 만능이 아니다. 연관 관계를 사용하는 시점에 결국 N+1 문제가 발생한다.
2. 절대 Entity를 직접 노출 시키지 않는다. (DTO 사용)
3. 연관 관계를 사용할 때, 패치 조인을 사용한다.
	- 조인 시 fetch를 사용하면 연관 객체까지 Inner Join 하여 한 쿼리로 가져온다.
	- 그냥 Join 시 연관 객체 값을 미리 가져오지 않는다.

### 원하는 값만 쿼리로 뽑아서 DTO로 변환하면 안되나?

Entity를 거치지 않고, 일반 쿼리문 처럼 값을 선택해서 가져오면 DB I/O 성능이 조금 좋아진다.
그러나 재사용성이 떨어지고 DTO가 Repository에 들아가면 API와 DB에 의존성이 생기기 때문에 좋지 않다.

```java
public List<OrderSimpleQueryDto> findOrderDtos() {
    return em.createQuery(
            "select new package.repository.order
              .simplequery.OrderSimpleQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
                " from Order o" +
                " join o.member m" +
                " join o.delivery d", OrderSimpleQueryDto.class)
        .getResultList();
}
```


