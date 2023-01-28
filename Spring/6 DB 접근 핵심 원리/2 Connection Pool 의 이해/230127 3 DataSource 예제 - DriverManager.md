# DataSource 예제 - DriverManager

```java
package hello.jdbc.connection;

import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;

@Slf4j
public class ConnectionTest {

    // DriverManager 를 사용한 Connection 획득
    @Test
    void driverManager() throws SQLException {
        Connection con1 = DriverManager.getConnection(URL, USERNAME, PASSWORD);
        Connection con2 = DriverManager.getConnection(URL, USERNAME, PASSWORD);
        log.info("Connection = {}, Class = {}", con1, con1.getClass());
        log.info("Connection = {}, Class = {}", con2, con2.getClass());
    }

    // dataSourceDriverManager 를 사용한 Connection 획득
    // 호출을 받으면 항상 새로운 Connection 을 획득함
    @Test
    void dataSourceDriverManager() throws SQLException {
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        useDataSource(dataSource);
    }

    private void useDataSource(DataSource dataSource) throws SQLException {
        Connection con1 = dataSource.getConnection();
        Connection con2 = dataSource.getConnection();
        log.info("Connection = {}, Class = {}", con1, con1.getClass());
        log.info("Connection = {}, Class = {}", con2, con2.getClass());
    }
}
```

결과는 같지만 두 방식은 큰 차이점이 있다.

### 📍차이점

- DriverManager 는 Connection 이 필요할 때 마다 parameter 를 계속 전달해야한다.
- DriverManagerDataSource 는 첫 세팅 후 단순 호출로 Connection 을 획득할 수 있다.
- DriverManagerDataSource 는 설정과 사용이 분리되어 있다.

### ⚠️ 설정과 사용

- 설정은 필요한 속성을 입력하는 절차이다.
    - parameter 값을 입력하는 과정
    - 향후 코드 수정이 편리해진다.
- 사용은 설정을 신경쓰지 않고 설정된 변수를 호출해서 사용하는 방법이다.