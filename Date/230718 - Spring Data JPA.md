## 스프링 데이터 JPA가 제공하는 쿼리 메소드 기능

- 조회 : find..By, read...By, query...By, get...By
- Count : count...By 반환타입 `long`
- Exists : exists...By 반환타입 `boolean`
- 삭제 : delete...By, remove...By 반환 타입 `long`
- DISTINCT : findDistinct, findMemberDistinctBy
- LIMIT : findFirst3, findFIrst, findTop, findTop3

## 스프링 데이터 JPA 장점

- 위 기능은 엔티티의 필드명이 변경되면 인터페이스에 정의한 메서드 이름도 꼭 함께 변경해야 한다. 그렇지 않으면 애플리케이션을 시작하는 시점에 오류가 발생한다.
- 이렇게 애플리케이션 로딩 시점에 오류를 인지 할 수 있는 것이 스프링 데이터 JPA 의 매우 큰 장점이다.

