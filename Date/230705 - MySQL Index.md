## 인덱스 정리

### 인덱스 개념

인덱스는 테이블의 동작 속도(조회)를 높여주는 자료구조이다. 인덱스로 데이터의 위치를 빠르게 찾아주는 역할이다.

인덱스는 MYI(MySQL Index) 파일에 저장되고 인덱스가 설정되지 않으면 Table Full Scan이 일어나 성능이 저하되고나 치명적인 장애가 발생한다.

조회 속도는 빨라지지만 UPDATE, INSERT, DELETE 속도는 저하된다는 단점이 있다. (Table의 index 색인 정보를 갱신하는 추가적인 비용 소요)

때문에 효율적인 인덱스 설계로 단점을 최대한 보완하는 방법을 생각해야 한다.


### 인덱스 특징

인덱스는 하나 혹은 여러 개의 컬럼에 대해 설정할 수 있다. (단일 인덱스 or 여러 컬럼을 묶은 복합 인덱스)

WHERE 절을 사용하지 않고 인덱스가 걸린 컬럼을 조회하는 것은 성능에 아무런 영향이 없다.


### ORDER BY 와 GROUP BY에 대한 INDEX

인덱스는 ORDER BY와 GROUP BY에도 영향을 끼치는데 다음과 같은 경우에는 INDEX를 타지 않는다.

- ORDER BY 인덱스 컬럼1, 컬럼2 : 복수의 키에 대해서 ORDER BY를 사용한 경우
- WHERE 컬럼1='값' ORDER BY 인덱스 컬럼 : 연속하지 않은 컬럼에 대해서 ORDER BY를 사용한 경우
- ORDER BY 인덱스 컬럼1 DESC, 인덱스 컬럼2 ASC : DESC와 ASC를 혼합해서 사용한 경우
- GROUP BY 컬럼1 ORDER BY 컬럼2 : GROUP BY와 ORDER BY의 컬럼이 다른 경우
- ORDER BY ABS(컬럼) : ORDER BY 절에 다른 표현을 사용한 경우


### 다중 컬럼 인덱스

다중 컬럼 인덱스는 두 개 이상의 필드를 조합해서 생성한 INDEX이다. 1번째 조건과 이를 만족하는 2번째 조건을 함께 INDEX해서 사용한다. (MySQL은 INDEX에 최대 15개 컬럼으로 구성 가능)

다중 컬럼 인덱스는 단일 컬럼 인덱스 보다 더 비효율적으로 INSERT/UPDATE/DELETE를 수행하기 때문에 신중해야 한다.

때문에 가급적 UPDATE가 안되는 값을 선정해야 한다.


### 단일 인덱스, 다중 컬럼 인덱스 차이점

- Table1
	- Index1 - name
	- Index2 - address
- Table2
	- Index1 - name, address

```mysql
SELECT * FROM table1 WHERE name='홍길동' AND address='경기도';
```

table1의 경우 각각 인덱스가 걸려있기 때문에 MySQL은 둘 중 어떤 컬럼의 수가 더 빠르게 검색되는지 판단 후 빠른쪽을 먼저 검색하고 그 다음 다른 컬럼을 검색하게 된다.

table2의 경우 바로 원하는 값을 찾는데 그 이유는 INDEX를 젖아할 때 name과 address를 같이 저장하기 때문이다. 이렇게 사용할 경우 table1보다 더 빠르게 검색할 수 있다.

```mysql
SELECT * FROM table2 WHERE address='경기도';
```

이 경우에는 다중 컬럼 인덱스로 설정되어 있던 name이 함께 검색되지 않으므로 INDEX의 효과를 볼 수가 없다.

하지만 조건 값을 name='홍길동'으로 준다면 BTree 자료구조 탐색으로 인해 name컬럼은 인덱스가 적용된다. 예를 들어 idx_name(name, address, age)일 때 `WHERE name=? AND address=?` 는 인덱스가 적용되지만 `WHERE name=? AND age=?` 에서 age 컬럼은 인덱스 적용이 되지 않는다.

다중 컬럼 인덱스를 사용할 때는 INDEX로 설정해준 제일 왼쪽 컬럼이 WHERE 절에 사용되어야 한다.

### 설계 방법

- 무조건 많이 설정하지 않는다. (테이블 당 3~5개가 적당. 목적에 따라 상이)
- 조회 시 자주 사용되는 컬럼
- 고유한 값 위주로 설계
- 카디널리티가 높을수록 좋다 (=한 컬럼이 갖고 있는 중복의 정도가 낮을수록 좋다)
- INDEX 키의 크기는 되도록 작게 설계
- PK, JOIN의 연결고리가 되는 컬럼
- 단일 인덱스 여러 개보다 다중 컬런 INDEX 생성 고려
- UPDATE가 빈번하지 않은 컬럼
- JOIN 시 자주 사용되는 컬럼
- INDEX를 생성할 때 가장 효율적인 자료형은 정수형 자료(가변 데이터는 비효율적)
