### @RequestBody, @RequestParam, @ModelAttribute 차이

*@RequestBody* 는 클라이언트가 전송하는 JSON 형태의 HTTP Body 내용을 MessageConverter를 통해 Java Object로 변환 시켜주는 역할을 한다.

값을 주입하지 않고 값을 변환 시키므로(Reflection을 사용해 할당), 변수들의 생성자, Getter, Setter가 없어도 정상적으로 할당된다.


*@RequestParam* 은 1개의 HTTP 요청 파라미터를 받기 위해 사용한다. 필수 여부가 true 이기 때문에, 기본적으로 반드시 해당 파라미터가 전송되어야 한다. 전송되지 않으면 400 Error를 유발할 수 있으며, 반드시 필요한 변수가 아니라면 required 값을 false로 설정 해주어야 한다.


*@ModelAttribute* 는 HTTP Body 내용과 HTTP 파라미터의 값들을 생성자, Getter, Setter를 통해 주입하기 위해 사용한다.

값 변환이 아닌 값을 주입 시키므로 변수들의 생성자나 Getter, Setter 가 없으면 변수들이 저장되지 않는다.
