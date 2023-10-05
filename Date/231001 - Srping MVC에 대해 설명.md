### Srping MVC에 대해 설명

MVC는 Model, View, Controller의 약자이고, 각 레이어간 기능을 구분하는데 중점을 둔 디자인 패턴이다.

- Model은 데이터 관리 및 비지니스 로직을 처리하는 부분 (DAO, DTO, Service 등)
- View는 비지니스 로직의 처리 결과를 통해 유저 인터페이스가 표현되는 구간이다. (html, jsp, tymeleaf, mustache 등 화면을 구성하기도 하고, REST Api로 서버가 구현된다면 JSON 응답으로 구성되기도 한다.)
- Controller는 사용자의 요청을 처리하고 Model, View를 중개하는 역할을 한다. Model과 View는 서로 연결되어 있지 않기 때문에 Controller가 사이에서 통신 매체가 되어준다.
