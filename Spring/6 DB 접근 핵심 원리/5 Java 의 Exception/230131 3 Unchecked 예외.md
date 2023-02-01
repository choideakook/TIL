# Unchecked 예외

- Unchecked 예외는 Runtime 예외와 그 하위 예외를 뜻한다.
- 말 그대로 컴파일러가 예외를 체크하지 않겠다는 뜻이다.
- 체크 예외과 기본적으로 동일하지만,
Throws 를 선언하지않고 생략할 수 있다.
    - 이 경우 자동으로 예외가 Throw 된다.

⚠️ Thorw 를 생략 할 수 있느냐 없느냐가 둘의 차이점 이다.

<br>

## ✏️ Unchecked 예외 처리하기

### 📍 Throw

- 연습용 예외 객체 MyUncheckedException 을 생성했다.
- Unchecked Exception 을 던질 땐 별도의 선언이 필요하지 않다..
    - Throw 생략 가능

```java
package hello.jdbc.excepiton;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class UncheckedTest {

    /**
     * RuntimeException 을 상속받은 예외는 언체크 예외가 된다.
     */
    static class MyUncheckedException extends RuntimeException {
        public MyUncheckedException(String message) {
            super(message);
        }
    }

    static class Repository {
        public void call() {
            throw new MyUncheckedException("ex");
        }
    }    
}
```

### 📍Catch

예외를 처리하는 로직은 Checked 예외와 동일하다.

```java
static class Service {
        Repository repository = new Repository();

        // 예외 처리 method
        public void callCatch() {
            try {
                repository.call();
            } catch (MyUncheckedException e) {
                // 예외 처리 로직
                log.info("예외처리, message = {}", e.getMessage(), e);
            }
        }
    }

    // catch method 호출
    // 예외를 처리했기 때문에 호출한 쪽에서는 정상적으로 로직이 작동된다.
    @Test
    void unchecked_catch() {
        Service service = new Service();
        service.callCatch();
    }
```

<br>

## ✏️ Test

### 📍 Catch

- 정상적으로 실행 되었다.

```java
11:26:20.096 [main] INFO hello.jdbc.excepiton.UncheckedTest - 예외처리, message = ex
hello.jdbc.excepiton.UncheckedTest$MyUncheckedException: ex
	at hello.jdbc.excepiton.UncheckedTest$Repository.call(UncheckedTest.java:41)
	at hello.jdbc.excepiton.UncheckedTest$Service.callCatch(UncheckedTest.java:30)
	...
```

<br>

### 📍 Throw

- Throw 선언 생략 가능
- 정상적으로 test 가 작동된다.
    - 호출한 쪽에서 예외가 발생함

```java
    static class Service {
        Repository repository = new Repository();

        // 예외 Throw
        public void callThrow() {
            repository.call();
        }
    }

    @Test
    void unchecked_throw() {
        Service service = new Service();
        Assertions.assertThatThrownBy(() -> service.callThrow())
                .isInstanceOf(MyUncheckedException.class);
    }
```

<br>

## ✏️ Unchecked 예외의 장단점

- 장점
    - 무시하고 싶은 예외를 무시할 수 있다.
        - Throw 생략 기능
    - 신경쓰고 싶지 않은 예외의 의존관계를 참조하지 않아도 된다.
- 단점
    - 컴파일러가 별도로 체크해주지 않아 개발자가 실수로 예외를 누락할 수 있다.