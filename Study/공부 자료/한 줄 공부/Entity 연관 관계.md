JPA Entity에서는 테이블 간 연간관계를 어떻게 표현할까?

## 양방향 관계

**음식 : 고객 = N : 1**

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "user")
    private List<Food> foodList = new ArrayList<>();
}
```

- 한 명의 고객은 여러 번 주문이 가능한 상황이다.
	- Entity에서 여러 번 가능함을 `List<Food> foodList = new ArrayList<>();` 이런 식으로 표현할 수 있다.
- DB 테이블에서 고객 테이블 기준으로 음식의 정보를 조회할 때 JOIN을 사용하여 바로 조회가 가능하지만, 고객 Entity 입장에서 음식 Entity 정보를 가지고 있지 않으면 음식의 정보를 조회할 방법이 없다.\
- 실제 DB에는 컬럼이 존재하지 않지만 다른 Entity 참조하기 위해 위와 같이 사용한다.
- 음식과 고객 Entity 서로 참조하고 있기 때문에 **양방향 관계**라고 부른다.

## 단방향 관계

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
}
```

- 음식 Entity에서만 고객 Entity를 참조할 수 있다.
- 이런 관계를 **단방향 관계**라고 부른다.

### 정리

- DB 테이블에서는 테이블 사이의 연관관계를 FK로 맺을 수 있고 방향 상관없이 조회가 가능하다.
- Entity에서는 상대 Entity를 참조하여 Entity 사이의 연관관계를 맺을 수 있다.
- 하지만 상대 Entity를 참조하지 않고 있다면(단방향) 상대 Entity를 조회할 수 있는 방법이 없다.
- 따라서 Entity에서는 DB 테이블에는 없는 방향의 개념이 존재한다.

---

## @OneToOne

- 1 대 1 관계를 맺어주는 역할을 한다.

### 단방향

- 음식 Entity가 외래 키의 주인인 경우

![[Pasted image 20240730122902.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @OneToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
}
```

### 양방향

- 양방향 관계에서는 외래 키의 주인을 지정해 줄 때 `mappedBy` 옵션을 사용한다.
	- mappedBy 속성 값은 외래 키의 주인인 상대 Entity의 필드 명을 의미한다.

![[Pasted image 20240730123801.png]]

- 음식 Entity가 외래 키의 주인인 경우
```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @OneToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToOne(mappedBy = "user")
    private Food food;
}
```

---
## @ManyToOne

- N : 1 관계를 맺어주는 역할을 한다.

### 단방향

![[Pasted image 20240730141846.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
}
```

### 양방향

![[Pasted image 20240730141925.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "user")
    private List<Food> foodList = new ArrayList<>();
}
```

---
## @OneToMany

### 단방향

![[Pasted image 20240801234123.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @OneToMany
    @JoinColumn(name = "food_id") // users 테이블에 food_id 컬럼
    private List<User> userList = new ArrayList<>();
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
}
```

- 외래키를 음식 Entity가 직접 가질 수 있다면 INSERT 발생 시 한 번에 처리할 수 있지만 실제 DB에서 외래 키를 고객테이블이 가지고 있기 때문에 추가적인 UPDATEA가 발생된다는 단점이 있다.

### 양방향

1대 N 관계에서 일반적으로 양방향 관계는 존재하지 않는다.

@ManyToOne은 mappedBy 속성을 제공하지 않는다. 따라서 @JoinColum의 insertable과 updateable 옵션을 false로 설정하여 양쪽 JOIN 설정하면 양방향처럼 설정할 수 있다.

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

		@ManyToOne
		@JoinColumn(name = "food_id", insertable = false, updatable = false)
		private Food food;
}
```

---
## @ManyToMany

### 단방향

- N : M 관계를 풀어내기 위해 중간 테이블을 사용한다.

![[Pasted image 20240801235455.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToMany
    @JoinTable(name = "orders", // 중간 테이블 생성
    joinColumns = @JoinColumn(name = "food_id"), // 현재 위치인 Food Entity 에서 중간 테이블로 조인할 컬럼 설정
    inverseJoinColumns = @JoinColumn(name = "user_id")) // 반대 위치인 User Entity 에서 중간 테이블로 조인할 컬럼 설정
    private List<User> userList = new ArrayList<>();
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
}
```

- 중간 테이블을 컨트롤하기 어렵기 때문에 추후에 중간 테이블의 변경이 일어날 경우 문제가 발생할 가능성이 있다.

### 양방향

![[Pasted image 20240801235503.png]]

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @ManyToMany
    @JoinTable(name = "orders", // 중간 테이블 생성
    joinColumns = @JoinColumn(name = "food_id"), // 현재 위치인 Food Entity 에서 중간 테이블로 조인할 컬럼 설정
    inverseJoinColumns = @JoinColumn(name = "user_id")) // 반대 위치인 User Entity 에서 중간 테이블로 조인할 컬럼 설정
    private List<User> userList = new ArrayList<>();
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @ManyToMany(mappedBy = "userList")
    private List<Food> foodList = new ArrayList<>();
}
```

### 중간 테이블

![[Pasted image 20240801235659.png]]

중간 테이블을 직접 생성하여 관리하면 변경 발생 시 컨트롤하기 쉽기 때문에 확장성이 좋다.

```java
@Entity
@Table(name = "food")
public class Food {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double price;

    @OneToMany(mappedBy = "food")
    private List<Order> orderList = new ArrayList<>();
}
```

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "user")
    private List<Order> orderList = new ArrayList<>();
}
```

```java
@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "food_id")
    private Food food;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;
}
```

