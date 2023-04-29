## ResponseEntity

HTTP 응답을 빠르게 만들어주기 위한 객체이다.   
@ResponseBody 와 달리 Annotation이 아닌 객체로 사용된다. 즉, 응답으로 변환될 정보를 모두 담은 요소들을 객체로 만들어서 반환해준다. 객체의 구성요소에서 HttpMessageConverter는 응답이 되는 본문을 처리해준 뒤에, RESTTemplate 에 나머지 구성요소인 Staus를 넘겨 주게 된다. 