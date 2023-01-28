# DataSource 예제 - Connection Pool

## ✏️ HikariCP 를 사용한 Connection Pool

```java
    // Connection Pooling (커넥션 풀을 사용한 커넥션 획득)
    @Test
    void dataSourceConnectionPool() throws SQLException, InterruptedException {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl(URL);
        dataSource.setUsername(USERNAME);
        dataSource.setPassword(PASSWORD);
        // Connection 의 최대 갯수 설정
        dataSource.setMaximumPoolSize(10);
        // Pool 의 이름 명명
        dataSource.setPoolName("My Pool");

        useDataSource(dataSource);
        // Connection 생성 시간 대기
        Thread.sleep(1000);
    } 
```

- Connection Pool 에서 Connection 을 생성하는 작업은 application 실행 속도에 영향을 주지 않기 위해 별도의 thread 에서 작동하기 때문에 test 가 먼저 종료되어 버린다.
    - thread.sleep 을 통해 대기 시간을 주어 thread pool 에 Connection 이 생성되는 로그를 test 가 끝나기 전에 확인할 수 있다.
        - Connection 이 생성되는 시간이 너무 느려서 발생하는 현상임
    - sleep 을 안주면 test 가 너무 빨리 끝나서 sleep 으로 test 종료시간을 인위적으로 늘려 로그가 출력되는걸 확인할 수 있다.

### 🔍 출력물 마지막줄에서 Connection Pool 의 상태를 확인할 수 있다.

```java
20:12:34.644 [My Pool connection adder] DEBUG 
    com.zaxxer.hikari.pool.HikariPool 
    - My Pool - After adding stats (total=10, active=2, idle=8, waiting=0)
```

- Connection 총 10 개
- 연결 활성화된 Connection 2 개
- 연결 가능한 Connection 8 개
- 대기중인 Connection 0 개
    - 만약 요청된 Connection 이 10개 이상일 경우 waiting 으로 들어가고 사용중인 Connection 이 반환될 때 까지 기다리게 된다.