# 프로젝트 생성

### ⚠️ 프로젝트 생성할 때 Packaging - War 를 선택해야함!

- 보통은 Jar 를 선택하지만 이번 수업에서는 War 를 선택해서 프로젝트를 생성한다.
- War 는 톰캣을 별도로 직접 설치하고, JSP 를 사용하고 싶을 때 선택하는 옵션이다.
- build.gradle 의 plugins 에서 선택한 packaging 을 확인할 수 있다.

### 📍 Dependency

- spring web
- lombok

[🔗 intelliJ 초기 세팅](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Intellij%20프로젝트%20생성후%20기본%20세팅.md)

### ❗ 초기세팅시 Gradle - intelliJ 로 설정 하면 컴파일이 안됨

- 이유는 모르겠지만 설정을 바꾸면 컴파일이 되지 않기 때문에 gradle 로 설정후 프로젝트를 진행해야 한다.

<br>

### 📍 application.properties

- 요청과 응답 HTTP message 의 정보를 확인할 수 있는 기능 추가

```java
logging.level.org.apache.coyote.http11=debug
```

- 예제

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        System.out.println("HelloServlet.service");
        System.out.println("request = " + request);
        System.out.println("response = " + response);

        String username = request.getParameter("username");
        System.out.println("username = " + username);

        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        response.getWriter().write("hello" + username);
    }
}
```

🔍 출력물 확인

[🔗 HTTP Message](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/2%20HTTP%20개념과%20메서드/230120%201%20모든것이%20HTTP.md)

⚠️ 운영서버에 적용할경우 성능 저하가 발생하니 이 옵션은 개발할때만 적용하는것이 좋다.

```java
: Before fill(): parsingHeader: [true], parsingRequestLine: [true], parsingRequestLinePhase: [0], parsingRequestLineStart: [0], byteBuffer.position(): [0], byteBuffer.limit(): [0], end: [0]
// 시작라인
: Received [GET /hello?username=%EC%95%88%EB%85%95 HTTP/1.1
// 해더 정보들
Host: localhost:8080
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Upgrade-Insecure-Requests: 1
Cookie: Idea-59ed549d=e9f7163f-b84b-422b-b9e0-2e7da3360da5
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.1 Safari/605.1.15
Accept-Language: ko-KR,ko;q=0.9
Accept-Encoding: gzip, deflate
Connection: keep-alive

]
// intellij 출력물
HelloServlet.service
request = org.apache.catalina.connector.RequestFacade@40435c32
response = org.apache.catalina.connector.ResponseFacade@49ef3c94
username = 안녕
2023-02-13 10:13:49.994 DEBUG 40890 --- [nio-8080-exec-1] o.a.coyote.http11.Http11InputBuffer      : Before fill(): parsingHeader: [true], parsingRequestLine: [true], parsingRequestLinePhase: [0], parsingRequestLineStart: [0], byteBuffer.position(): [0], byteBuffer.limit(): [0], end: [456]
2023-02-13 10:13:49.994 DEBUG 40890 --- [nio-8080-exec-1] o.a.coyote.http11.Http11InputBuffer      : Received []
2023-02-13 10:13:49.994 DEBUG 40890 --- [nio-8080-exec-1] o.apache.coyote.http11.Http11Processor   : Socket: [org.apache.tomcat.util.net.NioEndpoint$NioSocketWrapper@65900340:org.apache.tomcat.util.net.NioChannel@bd547c:java.nio.channels.SocketChannel[connected local=/0:0:0:0:0:0:0:1:8080 remote=/0:0:0:0:0:0:0:1:65154]], Status in: [OPEN_READ], State out: [OPEN]
```

<br>

## ✏️ html 기본 세팅

- 경로 : main.webapp.index.html
    - 웰컴 페이지

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li><a href="basic.html">서블릿 basic</a></li>
</ul>
</body>
</html>
```

- 경로 : main.webapp.basic.html
    - 앞으로 공부할 자료들을 링크로 만들어 정리해놓은 페이지

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>hello 서블릿
        <ul>
            <li><a href="/hello?username=servlet">hello 서블릿 호출</a></li>
        </ul>
    </li>
    <li>HttpServletRequest
        <ul>
            <li><a href="/request-header">기본 사용법, Header 조회</a></li>
            <li>HTTP 요청 메시지 바디 조회
                <ul>
                    <li><a href="/request-param?username=hello&age=20">GET - 쿼리 파라미터</a></li>
                    <li><a href="/basic/hello-form.html">POST - HTML Form</a></li>
                    <li>HTTP API - MessageBody -> Postman 테스트</li>
                </ul>
            </li>
        </ul>
    </li>
    <li>HttpServletResponse
        <ul>
            <li><a href="/response-header">기본 사용법, Header 조회</a></li>
            <li>HTTP 응답 메시지 바디 조회
                <ul>
                    <li><a href="/response-html">HTML 응답</a></li>
                    <li><a href="/response-json">HTTP API JSON 응답</a></li>
                </ul>
            </li>
        </ul>
    </li>
</ul>
</body>
</html>
```
