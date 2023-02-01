# Unchecked 예외의 활용

checked 예외의 문제점을 Runtime 예외를 사용해 보완할 수 있다.

![s6551.png](Unchecked%20%E1%84%8B%E1%85%A8%E1%84%8B%E1%85%AC%E1%84%8B%E1%85%B4%20%E1%84%92%E1%85%AA%E1%86%AF%E1%84%8B%E1%85%AD%E1%86%BC%20578f180b84c14ed6ae81e5942a3cf77b/s6551.png)

- `SQLException` 과 `ConnectException` 을 RuntimeException 으로 변경해 주었다.
    - 별도로 throw 를 선언하지 않아도 해결하지 않은 예외를 자동으로 던지게 된다.

## ✏️ Runtime 예외로 변환해 문제 해결

기존 체크 예외를 Runtime 예외로 새로 생성해서 속성을 변경해줬다.

- 해당 객체가 처리할 수 없는 예외를 의존하지 않게 되었다.
- RuntimeSQLException 에서 생성자는 단순 message 만 출력하는것이 아닌 이전 예외의 massage 까지 출력해줄 수 있는 Cause Throwable 로 생성자를 만들었다.
- 🔗 예외 포함 스택 트레이스

```java
package hello.jdbc.excepiton;

import org.junit.jupiter.api.Test;
import java.sql.SQLException;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

public class CheckedAppTest {

    @Test
    void checked() {
        Controller controller = new Controller();
        assertThatThrownBy(() -> controller.request())
                .isInstanceOf(Exception.class);
    }
    // Throw 생략 가능
    static class Controller{
        Service service = new Service();

        public void request() {
            service.logic();
        }
    }
    static class Service{
        Repository repository = new Repository();
        NetworkClient networkClient = new NetworkClient();

        public void logic() {
            repository.call();
            networkClient.call();
        }
    }
    static class NetworkClient{
        public void call() {
            throw new RuntimeConnectException("연결 실패");
        }
    }
    static class Repository{
        public void call() {
            try {
                runSQL();
            } catch (SQLException e) {
                // RuntimeSQLException 을 만들 때
                // Cause Throwable 로 만들었기 때문에
                // 예외가 발생하면 이전 예외인 SQLException 의 설명이 출력되고
                // 속성은 RuntimeException 을 상속받아 Throw 를 선언하지 안아도 되게 변경되었다.
                throw new RuntimeSQLException(e);
            }
        }

        public void runSQL() throws SQLException {
            throw new SQLException("SQL Exception");
        }
    }
    // RuntimeException 로 변경
    static class RuntimeConnectException extends RuntimeException{
        public RuntimeConnectException(String message) {
            super(message);
        }
    }
    // RuntimeException 로 변경
    static class RuntimeSQLException extends RuntimeException {
        // 이전 예외까지 포함해서 message 를 출력해줌
        public RuntimeSQLException(Throwable cause) {
            super(cause);
        }
    }
}
```

<br>

### ⚠️ 사실 체크 예외의 문제점 때문에 최근 라이브러리들은 대부분 Runtime 예외를 기본으로 제공한다.

- 지금까지 공부한 JPA 기술도 Runtime 예외를 사용함
    - Spring 대부분 Runtime 으로 제공된다.

<br>

## ✏️ Runtime 예외의 문서화

런타임 예외는 놓칠 수 있기 때문에 문서화가 중요하다.

- 문서화를 꼼꼼하게 하거나, 코드에 Throws 런타임 예외를 남겨서 중요한 예외를 인지할 수 있게 해준다.