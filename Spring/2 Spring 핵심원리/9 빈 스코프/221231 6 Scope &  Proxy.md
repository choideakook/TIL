# 스코프와 프록시

ObjectProvider 을 사용하지 않아도 DL 을 매우 간략한 코드로 실행할 수 있는 방법이다.

- @Scope 의 가짜 프록시 Class 를 만들어두고 HTTP request 와 상관 없이 가짜 프록시 Class 를 다른 Bean 에 미리 주입하는 방식으로 작동된다.
- 진짜 객체 조회를 꼭 필요한 시점까지 지연처리 하는 기능이다.

## ✏️ 프록시 사용방법

### 📍 @Scope 옵션 추가

괄호에 proxyMode = ScopedProxyMode.TARGET_CLASS 를 추가해준다.

- @Scope 의 대상이 지금처럼 Class 인 경우 TARGET_CLASS 를 추가
- 대상이 interface 인 경우 INTERFACES 를 추가

```java
@Component
@Scope (value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
```

<br>

### 📍 DL 관련 로직 제거

MyLogger 를 의존하고 있는 Class 의 필드에 ObjectProvider 을 제거하고,

ObjectProvider 을 호출하는 getObject() 변수도 제거해준다.

- Controller

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    // MyLogger 주입 이 아닌 MyLogger 를 찾을 수 있는 (DL) 필드가 주입이 됨
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {

        String requestURL = request.getRequestURL().toString();

        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testID");
        return "OK";
    }
}
```

- Service

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;

    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

<br>

## ✏️ 프록시 작동 원리

@Scope 의 가짜 프록시 Class 를 만들어두고 HTTP request 와 상관 없이 가짜 프록시 Class 를 다른 Bean 에 미리 주입하는 방식으로 작동된다.

- 프록시 Class 확인 방법 : 초기화 전에 Class 를 호출하는 방식으로 확인가능

```java
@RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();

				// 초기화 전 myLogger class 호출
        System.out.println("myLogger = " + myLogger.getClass());

        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testID");
        return "OK";
    }
```

### 🔍 출력물

Spring 이 조작해서 만들어낸 프록시 Class 가 생성되있는걸 확인할 수 있다.

- CGLIB 라는 라이브러리로 진짜 Class 를 상속 받은 프록시 객체를 만들어 주입한다.

```java
myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$1d4a9204
[1f913bc9-a8d9-48b0-a7fd-1a346d610ef5] request scope bean create : hello.core.common.MyLogger@5b98e82
[1f913bc9-a8d9-48b0-a7fd-1a346d610ef5] [http://localhost:8080/log-demo] controller test
[1f913bc9-a8d9-48b0-a7fd-1a346d610ef5] [http://localhost:8080/log-demo] service id = testID
[1f913bc9-a8d9-48b0-a7fd-1a346d610ef5] request scope bean close : hello.core.common.MyLogger@5b98e82
```

가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.

- 프록시 Bean 은 내부에 실제 myLogger 를 찾아오는 로직이 들어있다.
- 프록시 객체는 원본 Class 를 상속받아 만들어졌기 때문에 프록시 객체를 사용하는 클라이언트는 원본인지 아닌지 구별 할 수 없이 동일하게 사용할 수 있다. (다형성)