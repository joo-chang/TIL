## 공통  인터페이스 분석

- JpaRepository 인터페이스 : 공통 CRUD 제공
- 제네릭은 <엔티티 타입, 식별자 타입> 설정

### 구성

![[Pasted image 20230717235438.png]]

`T findOne(ID) Optional<T> findById(ID)` 변경 
`boolean exists(ID) boolean existsById(ID)` 변경

---

### 제네릭 타입

- T : 엔티티
- ID : 엔티티의 식별자 타입
- S : 엔티티와 그 자식 타입

### 주요 메서드

- save(S) : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다.
- delete(T) : 엔티티 하나를 삭제한다. 내부에서 `EntityManaver.remove()` 호출
- findById(ID) : 엔티티 하나를 조회 한다. 내부에서 `EntityManger.find()` 호출
- getOne(ID) : 엔티티를 프록시로 조회한다. 내부에서 `EntityManger.getReference()` 호출
- findAll() : 모든 엔티티를 조회한다. 정렬이나 페이징 조건을 파라미터로 제공할 수 있다.