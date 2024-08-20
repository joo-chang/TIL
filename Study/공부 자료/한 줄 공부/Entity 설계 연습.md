## 상황

쇼핑몰에서 고객은 여러 개의 상품을 주문할 수 있다.
각 주문은 여러 상품을 포함하며, 각 상품은 하나의 주문에 속한다.
이 상황에서 주문과 상품의 관계는 다대다 관계이다. 
또한, 주문에는 고객 정보가 포함되며, 고객과 주문의 관계는 일대다 관계이다.

## 설계

### 고객 엔티티

- 고객은 여러 주문을 가질 수 있다.
- Customer와 Order는 일대다 관계

```java
@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String email;
    
    @OneToMany(mappedBy = "customer", cascade = CascadeType.ALL)
    private List<Order> orders = new ArrayList<>();
    
    // Getters and Setters
}
```

### 주문 엔티티

- 주문은 여러 상품을 포함하며, 각 상품은 주문에 속한다.
- Order와 OrderItem은 일대다 관계
- 주문은 하나의 고객과 연관된다.
- Order와 Customer는 다대일 관계

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "customer_id")
    private Customer customer;
    
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();
    
    // Getters and Setters
}
```

### 상품 엔티티

- 각각의 상품을 나타내며, 여러 `OrderItem`과 연관될 수 있다.

```java
@Entity
public class Item {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private int price;
    
    @OneToMany(mappedBy = "item")
    private List<OrderItem> orderItems = new ArrayList<>();
    
    // Getters and Setters
}
```

### 주문 상품 엔티티
\
- 주문 내의 개별 항목을 나타내며, 하나의 Order와 하나의 Item에 연관된다.

```java
@Entity
public class OrderItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "order_id")
    private Order order;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "item_id")
    private Item item;
    
    private int quantity;
    private int orderPrice;
    
    // Getters and Setters
}

```

## 테이블 구조

### Customer

```sql
CREATE TABLE customer (
    id BIGINT NOT NULL AUTO_INCREMENT, -- 고객의 고유 ID
    name VARCHAR(255), -- 고객 이름
    email VARCHAR(255), -- 고객 이메일
    PRIMARY KEY (id) -- 기본 키
);
```
### Item

```sql
CREATE TABLE item (
    id BIGINT NOT NULL AUTO_INCREMENT, -- 상품의 고유 ID
    name VARCHAR(255), -- 상품 이름
    price INT, -- 상품 가격
    PRIMARY KEY (id) -- 기본 키
);
```

### Order

```sql
CREATE TABLE orders (
    id BIGINT NOT NULL AUTO_INCREMENT, -- 주문의 고유 ID
    customer_id BIGINT, -- 고객 ID (외래 키)
    order_date TIMESTAMP, -- 주문 일시
    status VARCHAR(255), -- 주문 상태
    PRIMARY KEY (id), -- 기본 키
    FOREIGN KEY (customer_id) REFERENCES customer(id) -- 외래 키 제약 조건
);
```

### OrderItem

```sql
CREATE TABLE order_item (
    id BIGINT NOT NULL AUTO_INCREMENT, -- 주문 항목의 고유 ID
    order_id BIGINT, -- 주문 ID (외래 키)
    item_id BIGINT, -- 상품 ID (외래 키)
    quantity INT, -- 주문된 상품 수량
    order_price INT, -- 해당 상품의 주문 가격
    PRIMARY KEY (id), -- 기본 키
    FOREIGN KEY (order_id) REFERENCES orders(id), -- 외래 키 제약 조건
    FOREIGN KEY (item_id) REFERENCES item(id) -- 외래 키 제약 조건
);
```