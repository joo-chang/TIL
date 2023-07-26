## AOP (Aspect-Oriented Programming)

### AOP 용어

![[Pasted image 20230726085908.png]]

- Join Point
	- 추상적인 개념으로 advice가 적용될 수 있는 모든 위치를 말한다.
	- ex) 메서드 실행 시점, 생성자 호출 시점, 필드 값 접근 시점 등
	- 스프링 AOP는 프록시 방식을 사용하므로 조인 포인트는 `항상 메서드 실행 지점`
- Pointcut
	- 조인 포인트 중에서 advice가 적용될 위치를 선별하는 기능
	- 스프링 AOP는 프록시 기반이기 때문에 조인 포인트가 메서드 실행 시점밖에 없고 포인트 컷도 메서드 실행 지점만 가능
- Target
	- advice의 대상이 되는 객체
	- Pointcut으로 결정
- advice
	- 실질적인 부가 기능 로직을 정의하는 곳
	- 특정 조인 포인트에서 Aspect에 의해 취해지는 조치
- Aspect
	- advice + pointcut을 모듈화 한 것
- Advisor
	- 스프링 AOP에서만 사용되는 용어로  advice + pointcut 한 쌍
- Weaving
	- pointcut으로 결정한 타겟의 join point에 advice를 적용하는 것
- AOP 프록시
	- AOP 기능을 구현하기 위해 만든 프록시 객체
	- 스프링에서 AOP 프록시는 JDK 동적 프록시 또는 CGLIB 프록시
	- 스프링 AOP의 기본값은 CGLIB 프록시