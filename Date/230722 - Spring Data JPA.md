## 페이징과 정렬

### 페이징과 정렬 파라미터

- `org.springframework.data.domain.Sort` : 정렬 기능
- `org.springframework.data.domain.Pageable` : 페이징 기능 (내부에 Sort 포함)


### 특별한 반환 타입

- `org.springframework.data.domain.Page` : 추가 count 쿼리 결과를 포함하는 페이징
- `org.spirngframework.data.domain.Slice` : 추가 count 쿼리 없이 다음 페이지만 확인 가능 (내부적으로 limit + 1 조회, SNS 자동 더보기 같은 경우) 

### 페이징과 정렬 사용 예제

```java
//count 쿼리 사용 
Page<Member> findByUsername(String name, Pageable pageable); 
//count 쿼리 사용 안 함  
Slice<Member> findByUsername(String name, Pageable pageable); 
//count 쿼리 사용 안 함  
List<Member> findByUsername(String name, Pageable pageable); 
List<Member> findByUsername(String name, Sort sort);
```

```java
//when
PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username"));

Page<Member> page = memberRepository.findByAge(10, pageRequest);

```

### count 쿼리를 다음과 같이 분리할 수 있음

```java
  @Query(value = “select m from Member m”,
         countQuery = “select count(m.username) from Member m”)
  Page<Member> findMemberAllCountBy(Pageable pageable);
```

### 페이지를 유지하면서 엔티티를 DTO로 변환

```java
Page<Member> page = memberRepository.findByAge(10, pageRequest);

    Page<MemberDto> dtoPage = page.map(m -> new MemberDto());lpo 76 ÷33asdf
```