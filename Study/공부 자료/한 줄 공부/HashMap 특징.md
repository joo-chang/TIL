## 장점

### 빠른 검색

HashMap은 해시 함수를 사용하여 키와 값의 쌍을 저장하므로 특정 키에 대한 값을 상대적으로 빠르게 검색할 수 있다. 평균적으로 O(1)의 시간 복잡도를 가진다.


### 데이터 저장 및 조회

Key-Value 쌍을 저장하고 키를 사용하여 검색할 수 있으므로 데이터를 관리하고 검색하는 데 효율적이다.
데이터베이스와 유사한 구조를 사용할 수 있다.


### 높은 성능

내부적으로 해시 테이블을 사용하며, 해시 함수를 통해 키를 인덱싱한다. 이로 인해 데이터를 빠르게 엑세스할 수 있으며 높은 성능을 가진다.

### 다양한 용도

캐싱, 인덱싱, 데이터베이스와의 상호 작용, 빠른 데이터 검색 등 다양한 애플리케이션에서 활용된다.


### 확장성

데이터의 크기에 따라 동적으로 확장되므로 대용량 데이터를 다루기에 적합하다. 크기가 커져도 성능에 큰 영향을 미치지 않는다.

<br>

---

## 단점

### 해시 충돌

해시맵에서 여러 키가 동일한 해시 버킷에 매핑되는 해시 충돌이 발생할 수 있다.
이러한 충돌은 성능 저하를 일으킬 수 있다.


### 순서 보장 x

해시맵은 순서를 보장하지 않기 때문에 순서가 중요한 경우에 사용하기 적합하지 않을 수 있다.

순서가 필요할 땐 LinkedHashMap을 사용하면 된다.

### 동기화 문제

기본적으로 스레드로부터 안전하지 않기 떄문에 멀티스레드 환경에서 사용하는 겨웅 동기화가 필요할 수 있다.

ConcurrentHashMap을 사용하여 동시성 문제를 해결할 수 있다.

### 메모리 사용량

해시 테이블을 사용하므로 메모리를 상당히 많이 사용할 수 있다. 키와 값의 크기에 따라 메모리 사용량이 증가할 수 있다.
