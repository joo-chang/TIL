## ResponseEntity

HTTP 응답을 빠르게 만들어주기 위한 객체이다.   
@ResponseBody 와 달리 Annotation이 아닌 객체로 사용된다. 즉, 응답으로 변환될 정보를 모두 담은 요소들을 객체로 만들어서 반환해준다. 객체의 구성요소에서 HttpMessageConverter는 응답이 되는 본문을 처리해준 뒤에, RESTTemplate 에 나머지 구성요소인 Staus를 넘겨 주게 된다. 

```java
//ResponseEntity 선언 구조
public class ResponseEntity extends HttpEntity {

  private final Object status;
}
```

먼저 ResponseEntity의 구조는 status만 필드 값으로 가지고 있다. ResponseEntity에서 직접적으로 Status Code 를 지정할 수 있다는 것을 의미한다. 나머지 부분은 HttpEntity에 구현이 되어있다. 

```java
//HttpEntity 선언 구조
public class HttpEntity<T> {
    public static final HttpEntity<?> EMPTY = new HttpEntity<>();
  
  
    private final HttpHeaders headers;
  
    @Nullable
    private final T body;
}
```

ResponseEntity는 HttpEntity를 상속 받아 구현되는데 HttpEntity에서는 Generic 타입으로 Body 가 될 필드 값을 가질 수 있다. Generic 타입으로 인하여 바깥에서 Wrapping 될 타입을 지정할 수  있다. Wrapping 된 객체들은 자동으로 HTTP 규격에서 Body에 들어갈 수 있도록 반환된다. 또한, 필드 타입으로 HttpHeader를 가지고 있다. ResponseEntity는 ResponseBody와 다르게 객체 안에서 Header를 설정해 줄 수 있다.


위 개념을 활용해서 jwt Token 값을 헤더에 넣어서 보내 주는 기능 구현

```java
@RequestMapping(method = RequestMethod.POST, value = "/login", produces = { MediaType.APPLICATION_JSON_VALUE })  
public ResponseEntity<ProcessResult<Object>> userLogin(@RequestBody UserLoginReq userLoginReq, HttpServletRequest request) throws Exception {  
    ProcessResult<Object> processResult = null;  
    ProcessCode processCode = null;  
  
    HttpHeaders httpHeaders = null;  
    try {  
        UserLoginDto userLoginDto = userService.userLogin(userLoginReq, request);  
        // Header와 Body에 TokentDto 객체 생성하여 넣어주고 return        UserLoginResp userLoginResp = new UserLoginResp(userLoginDto.getUserNo());  
        httpHeaders = new HttpHeaders();  
        httpHeaders.add(JwtFilter.AUTHORIZATION_HEADER, "Bearer " + userLoginDto.getToken());  
        processCode = ProcessCode.findByProcessCode("SWIT000");  
  
        processResult = new ProcessResult<>(  
                processCode.getSuccessYn(),  
                processCode.getProcCd(),  
                processCode.getRsltMsg(),  
                userLoginResp  
        );  
    } catch (ServiceException e) {  
        processCode = ProcessCode.findByProcessCode(e.getProcCd());  
  
        processResult = new ProcessResult<>(  
                processCode.getSuccessYn(),  
                processCode.getProcCd(),  
                processCode.getRsltMsg(),  
                null        );  
    }  
  
  
    return new ResponseEntity<>(processResult, httpHeaders, HttpStatus.OK);  
}
```
