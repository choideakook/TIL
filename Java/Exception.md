# Exception

## ✏️ Exception 의 개념

Java 는 Error 와 Exception Class 로 관리하고 둘을 구분한다.

- Error
    - 수숩할 수 없는 심각한 문제
    - 개발자가 미리 예측해서 방지 할 수 없다.
- Exception
    - 개발자의 실수나 사용자의 영향에 의해 발생한 문제
    - 개발자가 미리 예측해 방지할 수 있다.

<img width="585" alt="jexc1" src="https://user-images.githubusercontent.com/115536240/215631431-7e06c2be-ed30-4bd0-b10a-6e810bf8b645.png">

Error 와 Exception 는 Throwable 을 상속하고 Throwable 은 최상의 Class 인 Object 를 상속하는 구조이다.

Exception 은 모든 예외를 상속하고 있는 예외의 최상위 Class 이다

Exception 은 RuntimeException 과 IOException 으로 한번 더 구분 된다.

- RuntimeException ( = Unchecked Exception)
    - 예외에 대한 조치를 하지 않아도 된다.
    - application 을 실행해야 Exception 을 확인할 수 있는 Exception
- IOException (Checked Exception)
    - RuntimeException 을 제외한 모든 Exception 을 Checked Exception 이라고 한다.
    - IOException 의 IO 는 Input , Output 을 뜻하고 data 를 IO 할 때 발생하는 Exception 을 뜻한다.
    - Checked Exception 은 무조건 예외에 대한 조치를 해줘야 한다.
        - Try , Catch
        - Throw …

## ✏️ Try , Catch 의 메커니즘

```java
    @Test
    void main() {
        System.out.println(1);
        System.out.println(2/0);
        System.out.println(3);
    }
```

Java 에서는 숫자를 0으로 나누는 기능을 지원하지 않는다.

위의 예제에서 가운데 출력 명령은 숫자 2 를 0 으로 나누고 있어 예외가 발생하게 된다.

```java
1
java.lang.ArithmeticException: / by zero
```

보다시피 예외가 발생했지만 첫번째 출력명령인 1 은 출력이 되었고 예외 발생 이후의 출력물 3 은 출력 되지 않았다.

<br>

Java 의 시스템에서는 정상적으로 처리하던 로직에서 예외가 발생할 경우 작업을 중단하고 예외 메시지를 출력하게 되어있다.

### 📍 기본적인 사용법

예외가 발생할 것 같은 로직에 Try , Catch 구문을 사용할 경우
예외가 발생하면 Catch 구문이 실행되고 다음 로직을 이어서 실행하게 된다.

```java
    @Test
    void main() {
        System.out.println(1);

        try {
            System.out.println(2 / 0);
        } catch (ArithmeticException e) {
            System.out.println("계산이 잘못되었습니다.");
        }

        System.out.println(3);
    }
```

출력물

```java
1
계산이 잘못되었습니다.
3
```

예외 발생 시 직접 작성한 출력물 말고 java 의 exception message 를 보고싶으면 e.getMessage 를 호출하면 된다.

```java
    @Test
    void main() {
        System.out.println(1);

        try {
            System.out.println(2 / 0);
        } catch (ArithmeticException e) {
            System.out.println("계산이 잘못되었습니다." + e.getMessage());
        }

        System.out.println(3);
    }
-- 출력물 --
1
계산이 잘못되었습니다./ by zero
3
```

### 📍 여러가지 예외가 예상되는 경우

예러가지 예외가 예상된다면 Catch 를 여러번 사용해 예외를 잡을 수 있다.

```java
    @Test
    void main() {
        int[] array = {0};

        System.out.println(1);

        try {
            System.out.println(2);
            System.out.println(2 / 0);
            System.out.println(3);
            System.out.println(array[1]);
        } catch (ArithmeticException e) {
            System.out.println("계산이 잘못되었습니다.");
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("존재하지 않는 index 입니다.");
        }

        System.out.println(3);
    }
```

출력물을 보면
try 절의 첫번째 출력 명령만 실행이 되고 예외가 발생한 부분부터 try 가 끝날 때 까지는 실행이 안되고 마지막 3 을 출력하고 있는 출력 명령이 실행된걸 확인할 수 있다.

```java
1
2
계산이 잘못되었습니다.
3
```

<br>

## ✏️ Finally

Java 는 외부의 data 와 연결되 data 를 요청하거나 응답 받아야 할 때가 있다.

이러한 외부의 data 플랫폼들을 Resource 라고 칭하고,
Resource 와 연결해 data 를 주고받을 때 수 많은 예외가 발생하게 된다.

- 이 때 발생하는 예외가 IOException 이다.

<img width="585" alt="jexc1" src="https://user-images.githubusercontent.com/115536240/215631692-b94440b1-7c29-47e2-9c8d-8ccc89b7e7ab.png">

Java 와 Resource 는 data 를 주고 받기 위해 연결과 연결 해제를 해야하는데,
예외를 처리하기 위해 Try , Catch 를 사용한다.

하지만 Try 도중에 예외가 발생될경우 바로 Catch 로 이동하게 되고 Try 의 남은 로직을 수행할수 없게되어 연결 해제인 Close() 가 실행되지 않아 연결이 유지된 상태로 Method 가 끝나버릴 수 있게된다.

<br>

이 문제를 해결하기 위해 Finally 문이 사용된다.

- finally 는 Exception 이 발생하던 하지 않던 마지막에 실행되는 기능이다.
- finally 에 Close() 를 넣어주면 Exception 이 발생하던 안하던 Connection 이 종료되게 된다.
- Close 는 Connection 이 되어있지 않으면 실행할 수 없기 때문에 대상이 되는 필드에 try 문 바깥에 null 로 생성하고 Try 문 안쪽에 값을 명시해주어야 한다.

```java
@Slf4j
public class MemberRepositoryV0 {

    public Member save(Member member) throws SQLException {

        String sql = "insert into member(member_id, money) values (?, ?)";

        // Close 를 실행하기 위한 명시적인 선언
        Connection con = null;
        PreparedStatement pstmt = null;

       // 핵심 로직 실행
        try {
            // Connection 실행
            con = getConnection();
            // Resource 를 사용한 data 접근이기 때문에 IOException 에 해당한다.
            // try , catch 문을 사용해 예외처리를 필수로 해야한다.
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, member.getMemberId());
            pstmt.setInt(2, member.getMoney());
            pstmt.executeUpdate();
            return member;

       // 예외 처리
        } catch (SQLException e) {
            log.error("db error", e);
            throw e;

       // Close 를 위한 Finally
        }finally {

            // 명시적으로 선언한 필드가 null 일경우 Close 를 할 수없어서 사용된 if 문
            if (stmt != null) {
                try {
                    // IOException 이 발생될 수 있는 로직이기 때문에
                    // 다시 Trt , Catch 문을 사용해야 한다.
                    stmt.close();
                } catch (SQLException e) {
                    log.info("error", e);
            }

            if (con != null) {
                try {
                    con.close();
                } catch (SQLException e) {
                    log.info("error", e);
            }
        }
    }
}
```
