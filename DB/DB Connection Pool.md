## 데이터베이스 커넥션 풀
- 애플리케이션 로딩 시점에 Connection 객체를 미리 생성하고, 애플리케이션에서 데이터베이스에 연결이 필요할 경우 미리 준비된 Connection 객체를 사용한다
- DB 연결 시 TCP/IP 핸드셰이크와 같은 여러 단계를 거쳐야 한다. 데이터베이스 커넥션 풀을 사용하여 DB 연결에 소요 시간을 절약하고 성능을 향상시킬 수 있다

### 데이터베이스 커넥션 풀의 동작 방식
1. 애플리케이션 서버가 시작될 때 일정 수의 커넥션을 미리 생성한다
2. 애플리케이션 요청에 따라 생성된 커넥션 객체를 전달한다
3. 일정 수 이상의 커넥션이 사용되면 새로운 커넥션을 만든다
4. 사용하지 않는 커넥션을 종료하고 최소한의 기본 커넥션을 유지한다

![image](https://github.com/user-attachments/assets/96b03f43-c047-42d8-babd-09c25d81adc6)

### 커넥션 풀의 장점
- 커넥션 객체를 미리 만들어 연결하여 메모리 상에 등록해두기 때문에 클라이언트가 빠르게 DB에 접속할 수 있다
- DB 커넥션 수를 제한함으로써 과도한 접속을 방지할 수 있다
- DB 접속 모듈을 공통화하여 DB 서버 환경이 변할 경우 비교적 쉬운 유지보수가 가능하다
- 연결이 끝난 커넥션을 재사용함으로써 새로 객체를 생성하는 비용을 줄일 수 있다

## Hikari CP
- 데이터베이스 연결(Connection)을 관리해 주는 도구(라이브러리)
- 커넥션 풀(Connection Pool)이 설정된 커넥션의 사이즈만큼의 연결을 허용하며 HTTP 요청에 대해 순차적으로 DB 커넥션을 처리해 주는 기능을 수행한다

- 자바: 기본적으로 `DataSource` 인터페이스를 사용하여 커넥션 풀을 관리한다
- 스프링: SpringBoot 2.0 이전에는 tomcat-jdbc를 사용하다가 2.0 이후 부터는 Hikari CP를 기본 옵션으로 채택한다
- Hikari CP 성능이 월등히 좋다

![image](https://github.com/user-attachments/assets/02ea8f4a-20e4-4b9c-966c-b49df0e028bf)

### Hikari CP의 동작 방식
1. Thread가 Connection을 요청하면 Connection Pool의 각자의 방식에 따라 유휴 Connection을 찾아서 반환한다
   - Hikari CP의 경우, 이전에 사용했던 Connection이 존재하는지 확인하고, 이를 우선적으로 반환한다

![image](https://github.com/user-attachments/assets/3ccc54a0-de34-4bb1-a375-bd8bc8508972)

2. 가능한 Connection이 존재하지 않으면, HandOffQueue를 Polling하면서 다른 Thread가 Connection을 반납하기를 기다린다. 만약 TimeOut 시간이 만료되면 예외를 던진다

![image](https://github.com/user-attachments/assets/53123ad7-5d56-426b-9a3b-0678af76e9f2)

3. 최종적으로 사용한 Connection을 반납하면 Connection Pool이 Connection 사용 내역을 기록하고, HandOffQueue에 반납된 Connection을 삽입한다. HandOffQueue를 Polling하던 Thread는 Connection을 획득하고 작업을 이어나간다

![image](https://github.com/user-attachments/assets/51b8f94a-bb0c-416c-b837-a8657dc14168)

## 데드락 피하기
- 커넥션 풀을 크게 설정하면? → 메모리 소모가 큰 대신 많은 사람의 대기시간이 줄어 든다
- 커넥션 풀을 작게 설정하면? → 사용자의 대기시간이 길어진다

- 커넥션 풀 사이즈를 지정할 때는 항상 데드락을 고려해야 한다
- [우아한 형제들](https://techblog.woowahan.com/2664/)에서 제시한 이론적으로 필요한 최소한의 커넥션 풀 사이즈는 다음과 같다
```
PoolSize = Tn × ( Cm -1 ) + 1

- Tn : 전체 Thread 갯수
- Cm : 하나의 Task에서 동시에 필요한 Connection 수
```

- 위의 경우는 데드락은 피할 수 있지만, 여유 커넥션이 +1 개 이므로 성능상 좋지 못할 수 있다. 커넥션 풀에 더욱 여유를 주고 싶다면 아래와 같이 해보자
```
PoolSize = Tn × ( Cm - 1 ) + ( Tn / 2 )

- thread count : 16
- simultaneous connection count : 2
- pool size : 16 * ( 2 – 1 ) + (16 / 2) = 24
```

## 환경 설정
1. 의존성 추가
```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jdbc'
}
```

2. 설정 추가
```java
@Configuration
public class DataSourceConfiguration  {

    @Bean
    @Primary
    @ConfigurationProperties("spring.datasource")
    public DataSourceProperties dataSourceProperties() {
        return new DataSourceProperties();
    }

    @Bean
    @ConfigurationProperties("spring.datasource.hikari")
    public HikariDataSource dataSource(DataSourceProperties properties) {
        return properties.initializeDataSourceBuilder().type(HikariDataSource.class).build();
    }
}
```

3. property 정의
```yaml
spring:
  datasource:
    url: jdbc:mysql://${MYSQL_URL}:${MYSQL_PORT}/${MYSQL_SCHEMA}
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: ${MYSQL_USERNAME}
    password: ${MYSQL_PASSWORD}
    hikari:
      connection-timeout: 3000
      validation-timeout: 3000
      minimum-idle: 5
      max-lifetime: 240000
      maximum-pool-size: 20
```
- `maximum-pool-size`: 최대 pool size (defailt 10)
- `connection-timeout`: 커넥션의 타임아웃
- `validation-timeout`: 유효한 타임아웃
- `minimum-idle`: 연결 풀에서 HikariCP가 유지 관리하는 최소 유휴 연결 수
- `idle-timeout`: 연결을 위한 최대 유휴 시간
- `max-lifetime`: 닫힌 후 pool 에있는 connection의 최대 수명 (ms)입니다.
 - `auto-commit`: auto commit 여부 (default true)

4. Hikari Pool이 제대로 동작하고 있는지 로깅 레벨 설정
```yaml
logging:
  level:
    org.hibernate.SQL: info
    root: info
    com.zaxxer.hikari.pool.HikariPool: debug
```

5. 실행 결과
![image](https://github.com/user-attachments/assets/ab18fd8c-f5f6-4b7e-9964-0afd0111d2fc)