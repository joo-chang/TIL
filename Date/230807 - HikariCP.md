## HikariCP?

> 빠르고, 간단하고, 믿을 수 있는 HikariCP는 "zero-overhead" 의 프로덕션 지원 JDBC 연결 풀이다. 라이브러리는 대략 130KB 이며 매우 가볍다.

- HikariCP는 데이터베이스 Connection을 관리해주는 라이브러리이다.
- HikariCP에서 커넥션 풀이 설정된 커넥션의 사이즈만큼 연결을 허용하고, HTTP 요청에 대해 순차적으로 DB 커넥션을 처리해주는 기능을 수행한다.
- DBCP (Database Connection Pool)이며, HikariCP, Common DBCP 등 라이브러리가 존재하는데, HikariCP는 가볍고 빠르게 처리할 수 있다는 장점이 있다.

> 오버헤드란?
> 
> - 어떤 처리를 하기 위해 들어가는 간접적인 처리 시간, 메모리 등을 의미한다.


### 데이터베이스 커넥션 풀 DBCP

#### JDBC (Java Database Connection) 과정

![[Pasted image 20230807103331.png]]

- JDBC 연결은 드라이버를 로드하고 연결하여 객체를 받아와야 하는 과정을 가지고 있다.
- 이 과정은 매번 사용자가 요청할 때마다 드라이버를 로드하고 커넥션 객체를 생성하여 연결하고 종료하는 과정이 불편하고 속도와 자원 소모에 대한 단점이 있다.
- 이를 보완하기 위해 DBCP를 사용한다.


#### DBCP

데이터베이스 커넥션 풀은 최초 Pool 내에서 연결들을 하여 HTTP 요청에 따라 응답을 제공하고 반환받아서 이를 재사용하는 것을 의미한다.

#### DBCP 과정

1. WAS 가 실행되면서 Pool 내에 Connection들을 생성해 둔다.
2. HTTP 요청이 오면 Pool 내에서 Connection 객체를 가져다가 사용한다.
3. 사용이 완료된 Connection 객체는 Pool 내에 반환한다.

#### DBCP 장점

1. WAS 와 DB와의 연결을 줄여서 비용을 줄인다.
2. Pool 내에 미리 연결 되어 있기 때문에 매번 생성하는 비용을 줄일 수 있다.
3. 연결에 대해서 조정을 할 수 있다.
4. Connection Pool을 크게 설정하면 메모리 소모가 큰 대신 많은 사람의 대기시간이 줄어든다.
5. Connection Pool을 적게 설정하면 사용자 대기 시간이 길어진다.


### HikariCP 환경 설정

기존 라이브러리 spring-boot-data-jdbc 내에 해당 HikariCP가 포함이 되어 있다.

#### application.yml

```
spring:  
  # PostgreSQL DB & Hikari DBCP 설정  
  datasource:  
    hikari:  
      driver-class-name: 
      jdbc-url:
      username: test  
      password: xxxxx  
      pool-name: Hikari Connection Pool  # Alias  
      maximum-pool-size: 20  
  
# HikariPool에 대한 로깅을 수행함  
logging:  
  level:  
    com.zaxxer.hikari.pool.HikariPool: debug
```

```java
import com.zaxxer.hikari.HikariConfig;  
import com.zaxxer.hikari.HikariDataSource;  
import org.apache.ibatis.session.SqlSessionFactory;  
import org.mybatis.spring.SqlSessionFactoryBean;  
import org.mybatis.spring.SqlSessionTemplate;  
import org.springframework.boot.context.properties.ConfigurationProperties;  
import org.springframework.context.ApplicationContext;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.context.annotation.PropertySource;  
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;  

import javax.sql.DataSource;  
  
@Configuration  
@PropertySource("classpath:/application.properties")  
public class DBConfig {  
    final  
    ApplicationContext applicationContext;  
  
    public DBConfig(ApplicationContext ac) {  
        this.applicationContext = ac;  
    }  
  
    @Bean  
    @ConfigurationProperties(prefix = "spring.datasource.hikari")  
    public HikariConfig hikariConfig() {  
        return new HikariConfig();  
    }  
  
    @Bean  
    public DataSource dataSource() {  
        return new HikariDataSource(hikariConfig());  
    }  
  
    @Bean  
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {  
        SqlSessionFactoryBean session = new SqlSessionFactoryBean();  
        session.setDataSource(dataSource);  
        session.setMapperLocations(applicationContext.getResources("classpath:mapper/*.xml"));  
        session.setTypeAliasesPackage("com.test.testApi.model");  
        session.setConfigLocation(new PathMatchingResourcePatternResolver().getResource("classpath:mybatis/mybatis-config.xml"));  
        return session.getObject();  
    }  
  
    @Bean  
    public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {  
        return new SqlSessionTemplate(sqlSessionFactory);  
    }  
}
```