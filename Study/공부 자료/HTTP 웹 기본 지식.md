### IP (인터넷 프로토콜 역할)

- 지정한 IP 주소에 데이터 전달
- 패킷이라는 통신 단위로 데이터 전달

### IP 프로토콜 한계

- 비연결성
	- 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송
- 비신뢰성
	- 패킷 유실
	- 패킷 전달 순서 문제 발생 
- 프로그램 구분
	- 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이면?

IP 프로토콜 한계를 해결해주는 게 TCP, UDP

---
### TCP

#### 인터넷 프로토콜 스택 4계층

- 애플리케이션 계층
	- HTTP, FTP
- 전송 계층
	- TCP, UDP
- 인터넷 계층
	- IP
- 네트워크 인터페이스 계층

![[Pasted image 20230922223310.png]]

ex) 채팅 프로그램에서 채팅 보내는 과정

1. 프로그램이 Hello, world 메세지 생성
2. Socket 라이브러리를 통해 전달
3. TCP 정보 생성, 메세지 데이터 포함
4. IP 패킷 생성, TCP 데이터 포함

#### TCP 특징

전송 제어 프로토콜(Transmission Control Protocol)

- 연결 지향 - TCP 3 way Handshake (가상 연결)
- 데이터 전달 보증
- 순서 보장

- 신뢰할 수 있는 프로토콜
- 현재 대부분 TCP 사용

#### TCP 3 way handshake

![[Pasted image 20230922224007.png]]

#### 순서 보장

![[Pasted image 20230922224453.png]]

### UDP

사용자 데이터그램 프로토콜(User Datagram Protocol)

- 하얀 도화지에 비유(기능이 거의 없다)
- 연결 지향 - TCP 3 way handshake X
- 데이터 전달 보증 X
- 순서 보장 X
- 데이터 전달 및 순서가 보장되지 않지만, 단순하고 빠르다.
- IP와 거의 같다. Port랑 체크섬 정도만 추가됨
- 애플리케이션에서 추가 작업이 필요하다.

---
### Port


- 0 ~ 65535 : 할당 가능
- 0 ~ 1023 : 잘 알려진 포트, 사용하지 않는 것이 좋다.
	- FTP - 20, 21
	- TELNET - 23
	- HTTP - 80
	- HTTPS - 443

### DNS

IP는 변경될 수 있다. 

도메인 네임 시스템(Domain Name System)

- 도메인 명을 IP 주소로 변환

---
### 인터넷 네트워크 정리

복잡한 인터넷 망에서 메세지를 보내기 위해서 먼저 인터넷 프로토콜 (IP)이 있어야 된다.

IP 프로토콜만 가지고 메세지가 잘 도착했는지 신뢰하기 어렵고 포트라는 개념도 없다. 그래서 메세지 순서가 꼬일 수도 있다.

이러한 문제를 TCP 프로토콜로 해결해준다. UDP는 거의 IP랑 똑같고 포트 정도만 추가 된다. 필요 시 UDP 프로토콜 위에 애플리케이션에서 필요한 기능을 확장할 수 있다.

포트는 같은 IP 안에서 동작하는 애플리케이션을 구분하기 위해 사용한다.

DNS는 IP는  변하기 쉽고 외우기 어려운데 도메인 명을 등록해서 사용할 수 있도록 도와주는 것이다.


---
### URI와 웹 브라우저 요청 흐름

URI(Uniform Resource Identifier)


URI는 로케이터(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다.

![[Pasted image 20230923204712.png]]

*리소스를 식별한다.*

URL - 리소스 위치
URN - 리소스 이름

### URI

- Uniform : 리소스 식별하는 통일된 방식
- Resource : 자원, URI로 식별할 수 있는 모든 것
- Identifier : 다른 항목과 구분하는데 필요한 정보


### URL

- `scheme://[userinfo@]host[:port][/path][?query][#fragment]`
- `https://www.google.com:443/search?q=hello&hl=ko`

- 주로 프로토콜 사용
- 프로토콜 : 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
	- http, https, ftp ...
- http는 80, https는 443

- 리소스 경로 (path), 계층적 구조
- query
	- key value 형태
	- ?로 시작, &로 추가 가능
	- query parameter, query string 으로 불림

---
