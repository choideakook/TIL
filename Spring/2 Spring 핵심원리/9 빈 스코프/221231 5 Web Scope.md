# Web Scope

- Web scope 는 웹 환경에서만 동작한다.
- 프로토타입과 다르게 Spring 이 해당 스코프의 종료시점까지 관리한다.
    - 소멸 콜백 method 가 호출된다.

## ✏️ Web Scope 의 종류

- request
    - HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프
    - 각각의 HTTP 요청마다 별도의 Bean Instance 가 생성되고 관리된다.
- session
    - HTTP Session 과 동일한 생명주기를 가지는 스코프
- application
    - 서블릿 컨텍스트와 동일한 생명주기를 가지는 스코프
- websocket
    - 웹 소켓과 동일한 생명주기를 가지는 스코프

<br>

## ✏️ request 스코프 사용 방법

HTTP request 를 요청하는 클라이언트 당 각각 할당되는 request 스코프

![IMG_0036.PNG](Web%20Scope%207aeb809c1e5247deadea1f208293e41f/IMG_0036.png)

프로토타입 스코프는 요청할 때 마다 각각 생성되었다면,

request 스코프는 클라이언트의 전용으로 생성된 스코프 객체가 HTTP 요청이 시작되고 나갈때 까지는 클라이언트 전용으로 같은 Intance 객체가 사용된다.

<br>

### 📍 라이브 러리 추가

gradle 에 spring-boot-starter-web 라이브러리를 추가해야 사용할 수 있다.

`implementation 'org.springframework.boot:spring-boot-starter-web'`

External Libraries 에 web 관련 라이브러리가 추가된걸 확인할 수 있다.

![스크린샷 2023-01-01 오후 9.37.36.png](Web%20Scope%207aeb809c1e5247deadea1f208293e41f/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-01-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.37.36.png)

Tomcat 도 라이브러리에 추가가 되어서 [localhost](http://localhost):8080 에 들어가면 web 페이지도 생성되는걸 볼 수 있다.

<br>

❗️ Spring boot 는 웹 라이브러리가 없으면 지금까지 사용한 AnnotationConfigApplicationContext 을 기반으로 어플리케이션을 구동하지만,

웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로AnnotationConfigServletWebServerApplicationContext 을 기반으로 애플리케이션을 구동한다.

<br>

### 📍 request Class 생성

동시에 여러 HTTP 요청이 들어오면 정확히 어떤 요청이 남긴 로그인지 구분하기어렵다.

이러한 문제를 해결하기 위해 request 스코프를 사용하면 된다.

```java
package hello.core.common;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.UUID;

@Component
@Scope (value = "request")
public class MyLogger {
		// 필드 생성
    private String uuid;
    private String requestURL;

		// requestURL 은 Bean 이 생성되는 시점에는 알 수 없으므로,
		// 외부에서 setter 로 입력받아야함
    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

		// 로그를 기록하기위한 출력 Method
    public void log(String message) {
        System.out.println("[" + uuid + "] [" + requestURL + "] " + message);
    }

    // PostConstruct 로 초기화 콜백 하면서 uuid 를 생성해줌
    @PostConstruct
    public void init () {
        uuid = UUID.randomUUID().toString();
        // 초기화 콜백 message
        System.out.println("[" + uuid + "] request scope bean create : " + this);
    }
    // 소멸 콜백 message (클라이언트가 web 을 종료함)
    @PreDestroy
    public void close (){
        System.out.println("[" + uuid + "] request scope bean close : " + this);
    }
}
```

<br>

### 💡 기대하는 공통 포맷 : [UUID] [requestURL] message

- UUID (유니크 아이디) 를 사용해서 HTTP 요청을 구분할 수 있다.
    - HTTP 요청이 오면 클라이언트에게 부여하는 고유하고 유일한 ID 값
    - 클라이언트가 입장하고 퇴장 할 때까지 같은 UUID 가 입력이된다.
- requestURL 정보도 추가로 넣어서 어떤 URL 을 요청해서 남은 로그인지 확인할 수 있다.

<br>

### 📍 Controller 생성

👉 [@RequiredArgsConstructor 사용법 (Lombo]([https://github.com/choideakook/TIL/blob/main/Spring/2 Spring 핵심원리/7 의존관계 자동 주입/221229 4 롬북과 최신 트랜드.md](https://github.com/choideakook/TIL/blob/main/Spring/2%20Spring%20%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC/7%20%EC%9D%98%EC%A1%B4%EA%B4%80%EA%B3%84%20%EC%9E%90%EB%8F%99%20%EC%A3%BC%EC%9E%85/221229%204%20%EB%A1%AC%EB%B6%81%EA%B3%BC%20%EC%B5%9C%EC%8B%A0%20%ED%8A%B8%EB%9E%9C%EB%93%9C.md))

👉 [@ResponseBody 사용법 (API 방식)]([https://github.com/choideakook/TIL/blob/main/Spring/1 Spring 입문/1 프로젝트 환경설정/221202 스프링 웹 개발 기초 (정적 컨텐츠 %2C MVC 방식 %2C API 방식).md](https://github.com/choideakook/TIL/blob/main/Spring/1%20Spring%20%EC%9E%85%EB%AC%B8/1%20%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%20%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95/221202%20%EC%8A%A4%ED%94%84%EB%A7%81%20%EC%9B%B9%20%EA%B0%9C%EB%B0%9C%20%EA%B8%B0%EC%B4%88%20(%EC%A0%95%EC%A0%81%20%EC%BB%A8%ED%85%90%EC%B8%A0%20%2C%20MVC%20%EB%B0%A9%EC%8B%9D%20%2C%20API%20%EB%B0%A9%EC%8B%9D).md))

```java
package hello.core.web;

import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

// My Logger 가 잘 작동하는지 확인하는 Controller
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo") // log-demo 라는 요청이 발생되면 실행되는 method
    @ResponseBody
    // HttpServletRequest 를 prameter 값에 넣어주면 고객 요청 정보를 받을 수 있다.
    public String logDemo(HttpServletRequest request) {
        // getRequestURL Method 는 고객이 어떤 url 을 사용했는지 확인하는 기능이다.
        String requestURL = request.getRequestURL().toString();
        // 고객이 사용한 url 을 myLogger 로 넘겨 request 스코프에 로그가 남도록 한다.
        myLogger.setRequestURL(requestURL);
        // 고객이 controler 서비스를 사용하고있는걸 message 로 전달해주고
        // message 값을 받은 log 가 출력물을 기록해준다.
        myLogger.log("controller test");
        logDemoService.logic("testID");
        return "OK";
    }
}
```

#### ❗️ requestURL 을 myLogger 에 set 하는 기능은 스프링 인터셉터나 서블릿 필터를 활용하는게 좋지만 아직 학습하지않아서 Controller 에 사용했음.

```java
// 이 로직은 컨트롤러 보다 인터셉터나 서블릿 필터가 더 적합하다.
String requestURL = request.getRequestURL().toString();
myLogger.setRequestURL(requestURL);
```

<br>

### 📍 Service 생성

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

### ❗️ request 는 클라이언트가 url 에 접속해야 생성이 되는데 Contianer 를 생성하는 시점에 HTTP request 요청이 없기때문에 주입할 스코프가 없어서 에러가 발생하게된다.

👉 [문제 해결 방법]()

### 🔍 문제 해결 후 출력물

1. 초기화 콜백 : request scope bean 이 생성됨
2. controller test
3. service test
4. 소멸 콜백

```java
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] request scope bean create : hello.core.common.MyLogger@613743f4
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] [http://localhost:8080/log-demo] controller test
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] [http://localhost:8080/log-demo] service id = testID
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] request scope bean close : hello.core.common.MyLogger@613743f4
```

<br>

❗️ 이 상태에서 새로고침을 하게되면 url 을 종료하고 다시 불러오기 때문에 UUID 가 바뀌는걸 확인할 수 있다.

```java

[0f889351-ab4c-4a12-b9fb-e7d55deea86a] request scope bean create : hello.core.common.MyLogger@613743f4
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] [http://localhost:8080/log-demo] controller test
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] [http://localhost:8080/log-demo] service id = testID
[0f889351-ab4c-4a12-b9fb-e7d55deea86a] request scope bean close : hello.core.common.MyLogger@613743f4
[1a5cbe4c-802c-4244-95c3-a3bde6f2a9c5] request scope bean create : hello.core.common.MyLogger@172b1dcb
[1a5cbe4c-802c-4244-95c3-a3bde6f2a9c5] [http://localhost:8080/log-demo] controller test
[1a5cbe4c-802c-4244-95c3-a3bde6f2a9c5] [http://localhost:8080/log-demo] service id = testID
[1a5cbe4c-802c-4244-95c3-a3bde6f2a9c5] request scope bean close : hello.core.common.MyLogger@172b1dcb
```

<br>

👉 [프록시 방식으로 문제 해결하기]()