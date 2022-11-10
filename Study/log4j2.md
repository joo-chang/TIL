### Configuration

로그 설정의 최상위 요소이다. 내부에 Properties, Appenders, Loggers 요소를 자식으로 가진다. status 속성은 log4j2 내부 이벤트에 대한 로그 레벨을 설정한다.   
애플리케이션 실행과 관련된 로그에는 영향을 끼치지 않는다.
<br>

### Properties

설정 파일 내부에서 사용할 변수이다. name 속성의 값을 설정 파일 내부에서 key 로 사용하여 해당 태그의 내용을 쉽게 재사용할 수 있다.
<br>

### Appenders

로그를 어디로 보낼지, 어떻게 처리할지 정의하는 곳이다. 보통 콘솔 확인을 위한 ConsoleAppender와 파일 저장을 위한 RollingFileAppender를 많이 사용한다.

#### ConsoleAppender

```
<Console name = "Console_Appender" target = "SYSTEM_OUT">
	<PatternLayout pattern="${LOG_PATTERN}" />
</Console>
```

Console은 이름대로 콘솔에 로그를 찍어주는 Appender이다. target 속성의 기본 값은 SYSTEM_OUT이고, SYSTEM_ERR로 바꿀 수 있다.
자식 요소로 PatternLayout이 있는데, pattern 속성의 값 형태로 로그를 생성한다.

