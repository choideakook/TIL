# UnsatisfiedDependencyException

Unsatisfied / Dependency / Exception

만족스럽지못한 / 의존관계 / 예외

<br>

즉 Application 을 실행할때 특정 필드의 DI 가 제대로 주입되지 않아 실패했을 때 발생하는 에러이다.

에러메세지를 잘 읽어보면 어느 Class 의 어느 필드가 문제인지 나오니 확인해서 에러를 해결하면된다.

 <br>

## ✏️ request Scope 실행시 해당 에러가 발생하는경우

👉 [문제의 코드 보기]()

request Scope 는 클라이언트가 url 에 접속해야 생성이 되는데 Contianer 를 생성하는 시점에 HTTP request 요청이 없기때문에 주입할 스코프가 없어서 에러가 발생하게된다.

```java
Error creating bean with name 'myLogger':
		Scope 'request' is not active for the current thread;

// myLogger 에 주입할 Scope 'request' 가 활서어화 되어있지 않다.
```

<br>

### 📍 해결방법_ ObjectProvider

진짜 객체 조회를 꼭 필요한 시점까지 지연처리 하는 기능이다.

👉 [ObjectProvider 의 다른 기능]()

- ObjectProvider 를 사용해 로직 수정

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    // MyLogger 주입 이 아닌 MyLogger 를 찾을 수 있는 (DL) 필드가 주입이 됨
    private final ObjectProvider <MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        // 변수 생성
        MyLogger myLogger = myLoggerProvider.getObject();

        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testID");
        return "OK";
    }
}
```

- 🔍 출력물 확인

똑같은 에러가 이번엔 logDemoService 에서 발생했음

- logDemoService 도 MyLogger 를 의존하고있기때문에 에러가 발생함
    - ObjectProvider 로 문제를 해결하면된다.

```java
UnsatisfiedDependencyException: 
		Error creating bean with name 'logDemoService' defined in file
```

- logDemoService 수정

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider <MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

정상적으로 잘 작동된다.

[localhost:8080/log-demo](http://localhost:8080/log-demo) 에 접속해서 OK 가 출력되면 성공

👉 [더 간편한 프록시 방식으로 문제 해결하기]()