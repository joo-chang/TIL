## ResponseEntity

웹 서비스에서는 많은 정보를 송수신하게 된다. 각각 다른 웹 서비스들이 대화하려면, 정해진 약속에 맞게 데이터를 가공해서 보내야한다. 보내는 요청 및 데이터의 형식을 HTTP(HyperText Transport Protocol) 이라 한다.

Spring 에서도 HTTP에 맞게 데이터를 송수신 하는데, 요청에 대한 응답을 대신 만들어주는 ResponseEntity를 통해 빠르고 쉽게 HTTP 응답을 생성할 수 있다.

HTTP는 Client 와 Server 사이에 요청과 응답을 처리하기 위한 규약이다.   
HTTP 요청을 크게 세 가지로 구성된다. Start Line, Headers, Body

- Start Line
	- method, URL, Version으로 이루어져 있으며, 서버에서 요청을 받아들이는 첫 줄이다.
	- HTTP 버전과 함께 헤딩 요청에 대한 처리의 상태를 나타낸다. 200, 404 같은 코드
- Headers
	- 요청에 대한 접속 운영체제, 브라우저, 인증 정보와 같은 부가적인 정보를 담는다.
- Body
	- 요청에 관련된 json, html 과 같은 구체적인 내용을 포함한다.

Spring에서는 HTTP Response를 만드는 것이 주요 관심사이다. 200, 404 등 각각의 응답 상태 코드뿐만 아니라, Body에 들어갈 내용도 같이 넣어야한다.   
Spring에는 데이터를 받아서 자동으로 구성해주는 @ResponseBody와 @ResponseEntity 이다.

### @ResponseBody

@ResponseBody는 HTTP 규격에 맞는 응답을 만들어주기 위한 Annotation이다.    
HTTP 요청을 객체로 변환하거나, 객체를 응답으로 변환하는 HttpMessageConverter를 사용한다. HttpMessageConverter는 해당 Annotation이 붙은 대상을 response body에 직렬화 하는 방식으로 작동 된다. 따라서 Controller에서 반환할 객체나 Method에 @ResponseBody를 붙히는 것만으로 HTTP 규격에 맞는 값을 반환할 수 있다.

Annotation을 추가하는 것으로 간단하게 처리할 수 있다는 점이 @ResponseBody의 장점이다. 또한, Controller 에 @RestController가 붙으면 @ResponseBody를 생략할 수 있다.   
단점은 HTTP 규격 구성 요소 중 하나인 Header에 대해서 유연하게 설정할 수 없다는 점이다. Status도 메서드 밖에서 Annotatioin 을 사용하여 설정 해주어야 한다. 이는 @ResponseBody 만 사용하면 별도의 뷰를 제공하지 않고 데이터만 전송하는 형식이기 때문이다. 이를 해결해줄 수 있는 것이 ResponseEntity 라는 객체이다.

