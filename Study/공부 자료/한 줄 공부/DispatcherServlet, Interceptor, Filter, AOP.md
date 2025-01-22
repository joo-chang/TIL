## DispatcherServlet

- 스프링 MVC의 프론트 컨트롤러 역할을 수행하는 서블릿 클래스이다.
- 모든 HTTP 요청이 DispatcherServlet을 거쳐서, 적절한 Controller와 핸들러로 매핑되어 처리된다.

### 동작 흐름

1. 웹 서버에서 HTTP 요청을 DispatcherServlet에 전달한다.
2. DispatcherServlet은 스프링의 HandlerMapping을 통해 어떤 컨트롤러 메서드가 이 요청을 처리할지 찾는다.
3. 연결된 HandlerAdapter가 실제 컨트롤러 메서드를 호출해 로직을 수행한다.
4. 컨트롤러가 반환하는 뷰나 응답을 ViewResolver나 메세지 컨버터를 통해 최종 HTTP 응답 형태로 변환한다.
5. DispatcherServlet이 이 결과를 웹 서버에게 넘기고, 클라이언트에게 응답이 전송된다.

### 핵심 포인트

- DispatcherServlet은 스프링 MVC의 핵심이다. 요청 분배, 컨트롤러 호출, 뷰 렌더링 등 전반적인 제어를 담당한다.
- 스프링 부트에서 `/ (루트 경로)` 매핑으로 DispatcherServlet이 자동 등록된다.

<br>

## Interceptor (HandlerInterceptor)

- 스프링 MVC 에서 제공하는 핸들러 인터셉터로, 컨트롤러(핸들러) 호출 전후 혹은 뷰 렌더링 전후에 추가 처리를 하고 싶을 때 사용한다. ex) 인증/권한 체크, 로깅, 공통 세션 처리

### 동작 흐름

- DispatcherServlet이 *"이 요청은 A 컨트롤러가 처리해야겠다."* 라고 결정하기 전에, HandlerInterceptor를 preHandle 순서대로 실행
- 컨트롤러 로직이 끝난 후, postHandle과 afterCompletion 메서드를 순서대로 실행. (응답 반환 전후)
- preHandle에서 `return false;` 하면 요청 처리를 중단하고 직접 응답을 보낼 수 있다. (컨트롤러로 넘어가지 않는다.)

### 주요 메서드

- preHandle : 컨트롤러 호출 전. true/false 반환 (false면 흐름 중단)
- postHandle : 컨트롤러 처리 후, 뷰 렌더링 전
- afterCompletion : 뷰 렌더링까지 끝난 뒤(완료 후) 호출

### 등록 방식

- 주로 **WebMvcConfigurer**를 구현한 설정 클래스에서 `addInterceptors(InterceptorRegistry registry)` 메서드로 등록

<br>

## Filter (javax.servlet.Filter)

- 서블릿 스펙의 Filter이며, 스프링 MVC의 DispatcherServlet보다 더 앞에서 동작 가능(서블릿 컨테이너 레벨)
- 모든 요청/응답 흐름에 대해 공통 전처리, 후처리를 할 수 있는 기능
- ex) 인코딩 설정, 보안 검증, 로깅, CORS 처리 등

### 동작 흐름

- HTTP 요청이 웹 서버에 들어오면, Filter Chain의 등록 순서대로 각 Filter가 doFilter()를 실행한다.
- 각 Filter는 다음 Filter로 넘길지 결정할 수 있고, 다음 Filter 까지 다 끝난 후에 응답에 대해서도 후처리 할 수 있다.
- 최종적으로 DispatcherServlet(또는 다른 서블릿)에 도달하기 전까지 여러 Filter가 chaining 된다.

### 주요 메서드

- init(FilterConfig) : 초기화 시점(서버 시작)
- doFilter(SevletRequest, ServletResponse, FilterChain) : 요청-응답 처리의 핵심
- destroy() : 종료 시점(서버 종료)

### 등록 방식

- web.xml에 필터 태그로 설정하거나, Spring Boot 에서는 @WebFilter + FilterRegistrationBean으로 등록 가능
- 순서를 설정해 여러 Filter를 체인으로 구성한다.

## AOP (Aspect-Oriented Promramming)

- AOP는 관점 지향 프로그래밍이라는 뜻으로, 공통 기능을 분리해서 애플리케이션의 핵심 로직과 횡단 관심사를 분리하는 기법이다.
- 스프링에서 @Aspect + pointcut/advice 설정으로 메서드 호출 전후/예외 등에 로직을 삽입할 수 있다.

### 동작 시점

- 런타임에 프록시 객체를 생성하거나, 컴파일/클래스로드 시점에 코드 변환(스프링은 보통 런타임 프록시) 하여 특정 메서드 실행 전후에 어드바이스를 실행한다.

### 하는 일

- 트랜잭션 처리(@Transactional 내부도 AOP 원리), 로깅, 예외 처리, 보안 체크, 캐싱, 모니터링 등
- Interceptor나 Filter로도 할 수 있지만, **메서드** 단위나 **클래스** 단위의 공통 기능을 AOP 로 처리하면 더 정교하고 깔끔하게 분리 가능하다.

<br>

## 차이점 요약

### 동작 계층

- **Filter** : 서블릿 컨테이너 레벨(DispatcherServlet보다 앞). 모든 서블릿에 대해 동작할 수 있음
- **DispatcherServlet** : Spring MVC 프론트 컨트롤러. 요청이 필터 체인을 거친 뒤, DispatcherServlet으로 들어옴
- **Interceptor** : Spring MVC(DispatcherServlet) 내부에서 컨트롤러 전후 흐름 제어
- **AOP** : 스프링 빈의 메서드를 호출할 때, 프록시를 통해 공통 로직 삽입(주로 비즈니스 로직 전후)

### 사용 목적

- **Filter** : 인코딩, CORS, 공통 보안 체크, 로깅 등 전역적이고 서블릿 수준의 처리가 필요할 때
- **DispatcherServlet** : 스프링 Request -> Contorller -> View 로 이어지는 메인 흐름 담당
- **Interceptor** : 스프링 MVC 전용으로, 컨트롤러 진입 전후 로직, 공통 모델 추가, 인증/인가, 로깅, postHandle 등 핸들러 레벨의 처리
- **AOP** : 트랜잭션, 로깅, 캐싱, 예외 처리 등 메서드 단위 횡단 관심사

<br>

## 결론

- **Filter** : 서블릿 레벨에서 요청/응답을 전역적으로 가로채는 표준 메커니즘. 인코딩, 보안 등에 사용
- **DispatcherServlet** : 스프링 MVC 핵심 서블릿, 요청을 컨트롤러로 연결하고 응답 처리.
- **Interceptor** : 스프링 MVC에서 컨트롤러 전후 로직(특정 path)에 집중. 인증, 권한, 로깅 등.
- **AOP** : 비즈니스 로직(메서드) 전후/예외 시점에 횡단 기능 삽입. 트랜잭션, 로깅, 캐싱 등에 자주 사용.
