
application.yml 또는 application.properties 리소스 암호화 방법에 대해 알아보자.

## Jasypt 와 jasypt-spring-boot 라이브러리

Jasypt 라이브러리는 암호화에 대한 지식이 없어도 최소한의 비용으로 암호화 기능을 제공할 수 있도록 도와주는 라이브러리이다.
Jasypt 라이브러리를 사용하여 손쉽게 암복호화를 구현할 수 있다.

일반적으로 스프링 애플리케이션에서 appllication.yml or application.properties에 있는 리소스를 암호화해야 하는 경우에 주로 사용된다.

### jasypt-spring-boot 설정 방법

- @SpringBootApplication or @EnableAutoConfiguration를 사용중인 경우에 jasypt-spring-boot-starter 의존성을 추가해주면 된다.

#### 의존성 추가

```yml
implementation 'com.github.ulisesbocchio:jasypt-spring-boot-starter:3.0.5'
```

#### 설정 클래스 작성

리소스 암복호화를 위한 전용 키와 이를 활용하는 설정 클래스를 작성해주어야 한다.

```java
@Configuration
public class JasyptConfig {

	final static String KEY = "SECRET_KEY";

	final static String ALGORITHM = "PBEWithMD5AndDES";

	@Bean
	public StringEncryptor jasyptStringEncryptor() {
		PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
		SimpleStringPBEConfig config = new SimpleStringPBEConfig();
		config.setPassword(KEY);
		config.setAlgorithm(ALGORITHM);
		encryptor.setConfig(config);
		return encryptor;
	}

}
```

#### 암호화된 리소스 값으로 설정 값 수정하기

jasypt-spring-boot 라이브러리는 `ENC(암호화 값)` 으로 설정 된 부분을 복호화 한다.

아래 테스트를 이용하여 암호화 된 값을 받아 볼 수 있다.

```java
class JasyptConfigTest {

    @Test
    void privateKey암호화() throws IOException {
        ClassPathResource resource = new ClassPathResource("privatekey.txt");
        String privateKey = StreamUtils.copyToString(resource.getInputStream(), StandardCharsets.UTF_8);
        StandardPBEStringEncryptor standardPBEStringEncryptor = new StandardPBEStringEncryptor();
        standardPBEStringEncryptor.setAlgorithm(JasyptConfig.ALGORITHM);
        standardPBEStringEncryptor.setPassword(JasyptConfig.KEY);

        String enc = standardPBEStringEncryptor.encrypt(privateKey);
        System.out.printf("enc = ENC(%s)\\n", enc);
        System.out.printf("dec = %s\\n", standardPBEStringEncryptor.decrypt(enc));
    }

    @Test
    void string암호화() {
        String privateKey = "myKey";
        StandardPBEStringEncryptor standardPBEStringEncryptor = new StandardPBEStringEncryptor();
        standardPBEStringEncryptor.setAlgorithm(JasyptConfig.ALGORITHM);
        standardPBEStringEncryptor.setPassword(JasyptConfig.KEY);

        String enc = standardPBEStringEncryptor.encrypt(privateKey);
        System.out.printf("enc = ENC(%s)\\n", enc);
        System.out.printf("dec = %s\\n", standardPBEStringEncryptor.decrypt(enc));
    }

}

```

### 출처

https://mangkyu.tistory.com/284