## Session/Cookie vs Token

사용자 인증 방식에는 크게 Session/Cookie, Token 방식이 있다.

### Session / Cookie

Session 방식은 사용자가 로그인 요청을 보내면 사용자를 확인 후 Session ID를 발급하고 그 발급한 ID를 이용하여 다른 요청과 응답을 처리하는 방식이다.

![[Pasted image 20230514225823.png]]

이 경우 프로그램이 커져서 관리하는 Session이 늘어날 경우 별도로 세션 저장소를 관리해주어야 하는 번거로움이 있다.

---

### Token

Token 방식은 저장소 없이 로그인 시 토큰을 발급하고, 데이터 요청 시에 발급받은 토큰을 헤더를 통해 전달하여 응답받는 방식으로 진행된다.

![[Pasted image 20230514225959.png]]


### Token 방식 로그인 흐름

#### 1. 로그인으로 토큰(JWT) 발급

로그인을 통해 토큰을 발급 받는다. jjwt 라이브러리를 이용하여 JWT(JSON Web Token) 방식으로 생성한다.

![[Pasted image 20230514233722.png]]

JWT는 세 부분으로 나누어져 필요한 모든 정보를 자체적으로 지니고 있다는 특징이 있다.   
따라서 별도의 디비 접근 없이 해당 토큰의 유효성 검증이 가능하다.


#### 2. 발급 받은 토큰을 웹 스토리지에 저장

프론트에서 로그인 요청을 보내면 백엔드에서 토큰을 발급하여 응답으로 토큰을 보내준다. 그리고 토큰을 웹 스토리지에 저장한다.

Sotrage는 key-value의 형태로 데이터를 저장하는 저장소로, 하위 항목에  Local Storage, Session Stroage, Cookies가 있다.   
각 항목별로  정보의 휘발성, 보안 등 특징이 있고 개발자의 의도에 부합하는 특징을 가진 선택지를 고르면 된다.


#### 3. 저장된 토큰을 가지고 나의 정보 요청

로그인 후 Local Storage에 토큰이 저장되어 있는 상태이다. 프론트에서 토큰을 헤더에 담아 백엔드로 요청을 보내고 백엔드에서는 토큰 안에 담긴 정보를 확인하여 그에 해당하는 응답을 준다. 

#### Interceptor

![[Pasted image 20230515212539.png]]

인터셉터는 요청이 Controller로 가기 전에 요청을 가로채 작업을 처리할 수 있다. 여기서는 요청 안에 토큰이 있는지 확인하고, 토큰 안에 있는 내용을 디코딩하여 요청안에 다시 넣어주는 작업을 한다. 

---

### 구현

#### 1. 로그인으로 토큰 발급

1.1 프론트엔드에서 로그인 요청

```java
public class User {
	@Id
	private Long id;
	private String name;
	private String pwd;
}
```

```javascript
fetch("/login", {
	method: 'post',
	headers: {
		'content-type' : 'application/json'
	},
	body : JSON.stringify({
		name : $name.value,
		pwd : $pwd.value
	})
}).then(res => res.json())
	.then(token => {
		localStorage.setItem("jwt", token.accessToken)
		alert("로그인");
	})
```

fetch API로 사용자가 입력한 Id/pwd를 보내면 백엔드에서 생성한 토큰을 응답 받아 localStorage에 저장하는 구조이다.

1.2 Controller

```java

@RestController
public class LoginController{
	@PostMapping("/login")
	public ResponseEntity<TokenResponse> login(@RequestBody LoginRequest loginRequest) { 
		String token = userService.createToken(loginRequest); 
		return ResponseEntity.ok().body(
			new TokenResponse(token, "bearer")); 
	}
}
```

LoginRequest 객체를 만들어 프론트에서 보낸 정보를 받아 일련의 과정(UserService.createToken)을 거쳐 토큰을 생성하고, 생성된 토큰은 TokenResponse 객체로 감싸 프론트로 보내준다.

1.3 createToken

UserService.createToken

```java
public String createToken(LoginRequest loginRequest) {
	User user = userRepository.findByName(loginRequest.getName()) 
	.orElseThrow(IllegalArgumentException::new); 
	//비밀번호 확인 등의 유효성 검사 진행 
	return jwtTokenProvider.createToken(user.getName()); 
}
```

핵심 - jwtTokenProvider.createToken   
jwt 방식을 이용하여 토큰을 생성하려면 라이브러리를 추가해야 한다.

```
implementation 'io.jsonwebtoken:jjwt:0.9.1'
```

```java
@Component
public class JwtTokenProvider{
	private String secretKey;
	private long validityInMilliseconds;

	public JwtTokenProvider(@Value("${security.jwt.token.secret-key}") 
	String secretKey, @Value("${security.jwt.token.expire-length}") 
	long validityInMilliseconds){
		this.secretKey = 
		Base64.getEncoder().encodeToString(secretKey.getBytes());
		this.validityInMilliseconds = validityInMilliseconds;
	}

	// 토큰 생성
	public String createToken(String subject){
		Claims claims = Jwts.claims().setSubject(subject);
		Date now = new Date();
		Date validity = new Date(now.getTime() + validityInMilliseconds);
	
		return Jwts.builder() 
		.setClaims(claims)
		.setIssuedAt(now) 
		.setExpiration(validity) 
		.signWith(SignatureAlgorithm.HS256, secretKey) 
		.compact();
	}
  
	//토큰에서 값 추출 
	public String getSubject(String token) { 
		return Jwts.parser().setSigningKey(secretKey).
		parseClaimsJws(token).getBody().getSubject(); 
	}
	//유효한 토큰인지 확인 
	public boolean validateToken(String token) { 
		try { 
			Jws<Claims> claims = 
			Jwts.parser().setSigningKey(secretKey).parseClaimsJws(token); 
			if (claims.getBody().getExpiration().before(new Date())) { 
				return false; 
			} 
			return true; 
		} catch (JwtException | IllegalArgumentException e) { 
			return false; 
		} 
	}
}
```

```
application.properties

security.jwt.token.secret-key= secretsecretsecretsecretsecret security.jwt.token.expire-length= 3600000
```

1.4 Storage 에 토큰 저장

![[Pasted image 20230520210703.png]]

로그인 시 LocalStorage에 jwt라는 이름으로 토큰이 추가된다.

---

### 2. 토큰으로 내 정보 요청

2.1 프론트에서 내 정보 요청

fetch API를 이용하여 내 정보 요청

```javascript

fetch("/info", {
	method: 'get',
	headers: {
		'content-type': 'application/json',
		'Authorization': 'Bearer ' + localStorage.getItem("jwt")
	}
}).then(res => res.json())
	.then(json => alert("이름 : " + json.name + ", 비밀번호 : " + json.pwd))
```

```java

@GetMappint("/info")
public ResponseEntity<UserResponse> getUserFromToken(HttpServletRequest Request){
	String name = (String) request.getAttribute("name");
	User user = userService.findByName((String) request.getAttribute("name"));
	return ResponseEntity.ok().body(UserResponse.of(user));
}
```

프론트에서 요청 시 헤더에  **'Authorization': 'Bearer ' + localStorage.getItem("jwt")** 값을 넣어 전송한다.

2.2 Interceptor

요청이 controller에 도달하기 전에 Interceptor로 요청을 가로채 header에 포함된 토큰의 내용을 디코딩하여 그 내용을 다시 요청으로 담아 controller로 전달해 줘야한다.   
그러기 위해 어떤 요청에 대해서 인터셉터 할 것인지 interceptor를 등록해 놓아야 한다.   
WebMvcConfigurer 를 이용한다.

```java
@Configuration  
public class WebMvcConfig implements WebMvcConfigurer {  
    private final BearerAuthInterceptor bearerAuthInterceptor;  
  
    public WebMvcConfig(BearerAuthInterceptor bearerAuthInterceptor) {  
        this.bearerAuthInterceptor = bearerAuthInterceptor;  
    }  
  
    public void addInterceptors(InterceptorRegistry registry){  
        System.out.println(">>> 인터셉터 등록");  
        registry.addInterceptor(bearerAuthInterceptor).addPathPatterns("/info");  
    }  
}
}
```

WebMvcConfig.addInterceptors에서 '/info' 로 들어오는 요청에 대해 bearerAuthInterceptor를 등록해준다. 이렇게 하면 애플리케이션이 실행될 때 인터셉터를 등록하고 그 주소로 들어오는 요청을 기다리는 상태가 된다.

인터셉터를 등록하였고 구현
HandlerInterceptor를 Implements 하여 구현한다.

```java
@Component  
public class BearerAuthInterceptor implements HandlerInterceptor {  
    private AuthorizationExtractor authExtractor;  
    private JwtTokenProvider jwtTokenProvider;  
  
    public BearerAuthInterceptor(AuthorizationExtractor authExtractor, JwtTokenProvider jwtTokenProvider) {  
        this.authExtractor = authExtractor;  
        this.jwtTokenProvider = jwtTokenProvider;  
    }  
  
    @Override  
    public boolean preHandle(HttpServletRequest request,  
                             HttpServletResponse response, Object handler) {  
        System.out.println(">>> interceptor.preHandle 호출");  
        String token = authExtractor.extract(request, "Bearer");  
        if (StringUtils.isEmpty(token)) {  
            return true;  
        }  
  
        if (!jwtTokenProvider.validateToken(token)) {  
            throw new IllegalArgumentException("유효하지 않은 토큰");  
        }  
  
        String name = jwtTokenProvider.getSubject(token);  
        request.setAttribute("name", name);  
        return true;    
        
    }  
}
```

info라는 주소로 요청을 보내서 Interceptor가 호출되면 interceptor는 prehandle 메서드를 호출한다.   
preHandle 동작
- request로 부터 authExtractor.extract로 토큰을 추출
- jwtTokenProvider.getSubject로 토큰을 디코딩
- request.setAttribute로 요청에 디코딩한 값을 세팅

2.3 헤더에서 토큰 추출

```java
@Component  
public class AuthorizationExtractor {  
    public static final String AUTHORIZATION = "Authorization";  
    public static final String ACCESS_TOKEN_TYPE = AuthorizationExtractor.class.getSimpleName() + ".ACCESS_TOKEN_TYPE";  
  
    public String extract(HttpServletRequest request, String type) {  
        Enumeration<String> headers = request.getHeaders(AUTHORIZATION);  
        while (headers.hasMoreElements()) {  
            String value = headers.nextElement();  
            if (value.toLowerCase().startsWith(type.toLowerCase())) {  
                return value.substring(type.length()).trim();  
            }  
        }  
  
        return Strings.EMPTY;  
    }  
}
```

request 헤더 중에 'Authorization' 항목의 값을 가져와서 그 안에 토큰 타입을 제외한 토큰 자체를 가져오는 로직이다.

2.4 Controller

헤더에 토큰을 담아 요청을 보냈고, 요청을 가로채 토큰을 확인한 다음 요청에 그 토큰이 의미하는 값을 담아 주었다. 이후 controller에서 그 값을 받아 응답을 내려주면 된다.

```java
@GetMappint("/info")
public ResponseEntity<UserResponse> getUserFromToken(HttpServletRequest request){
	String name = (String) request.getAttribute("name");
	User user = userService.findByName(name);
	return ResponseEntity.ok().body(UserResponse.of(user));
}
```
