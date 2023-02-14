# HttpServletRequest 기본 사용법

## ✏️ HttpServletRequest 가 제공하는 기본 기능

### 📍 Start Line 정보

```java
package com.example.servlet.basic.request;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "requestHeaderServlet", urlPatterns = "/request-header")
public class RequestHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        printStartLine(request);
    }

    private static void printStartLine(HttpServletRequest request) {
        System.out.println("--- REQUEST-LINE - start ---");

        // GET
        System.out.println("request.getMethod() = " + request.getMethod()); 
        // HTTP/1.1
        System.out.println("request.getProtocol() = " + request.getProtocol()); 
        // http
        System.out.println("request.getScheme() = " + request.getScheme()); 
        // http://localhost:8080/request-header
        System.out.println("request.getRequestURL() = " + request.getRequestURL());

        // /request-header
        System.out.println("request.getRequestURI() = " + request.getRequestURI());
        //username=hi
        System.out.println("request.getQueryString() = " + request.getQueryString());
         //https 사용 유무
        System.out.println("request.isSecure() = " + request.isSecure());
        System.out.println("--- REQUEST-LINE - end ---");
        System.out.println();
    }
}
```

<br>

🔍 출력물 확인

```java
--- REQUEST-LINE - start ---
request.getMethod() = GET
request.getProtocol() = HTTP/1.1
request.getScheme() = http
request.getRequestURL() = http://localhost:8080/request-header
request.getRequestURI() = /request-header
request.getQueryString() = username=hello
// https 인가? http 인가?
// http 는 false
request.isSecure() = false
--- REQUEST-LINE - end ---
```

<br>

### 📍 해더 정보

```java
    private void printHeaders(HttpServletRequest request) {
        System.out.println("--- Headers - start ---");

        // request.getHeaderNames() 는
        // Http 에 있는 모든 header 정보를 전부 get 할 수 있음
        request.getHeaderNames().asIterator()
                .forEachRemaining(
                    headerName-> System.out.println(
                        headerName + " = " + request.getHeader(headerName)));

        System.out.println("--- Headers - end ---");
        System.out.println();
    }
```

<br>

🔍 출력물 확인

```java
--- Headers - start ---
host = localhost:8080
cookie = Idea-59ed549d=e9f7163f-b84b-422b-b9e0-2e7da3360da5
connection = keep-alive
upgrade-insecure-requests = 1
accept = text/html,application/xhtml+xml,application/xml;q=0.9,**;q=0.8
user-agent = Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/16.1 Safari/605.1.15
referer = http://localhost:8080/basic.html
accept-language = ko-KR,ko;q=0.9
accept-encoding = gzip, deflate
--- Headers - end ---
```

<br>

⚠️ 원하는 값만 출력하고 싶을경우

```java
// 원하는 정보가 Host 일 경우
request.getHeader("host");
// 이렇게 특정 data 만 get 가능
```

### 📍 Header 편리한 조회

```java
    private void printHeaderUtils(HttpServletRequest request) {
        System.out.println("--- Header 편의 조회 start ---");
        System.out.println("[Host 편의 조회]");
        System.out.println("request.getServerName() = " + request.getServerName()); //Host 헤더
        System.out.println("request.getServerPort() = " + request.getServerPort()); //Host 헤더
        System.out.println();

        System.out.println("[Accept-Language 편의 조회]");
        request.getLocales().asIterator()
                .forEachRemaining(locale -> System.out.println("locale = " + locale));
                        System.out.println("request.getLocale() = " + request.getLocale());
        System.out.println();

        System.out.println("[cookie 편의 조회]");
        if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            } 
        }
        System.out.println();

        System.out.println("[Content 편의 조회]");
        System.out.println("request.getContentType() = " + request.getContentType());
        System.out.println("request.getContentLength() = " + request.getContentLength());
        System.out.println("request.getCharacterEncoding() = " + request.getCharacterEncoding());
        System.out.println("--- Header 편의 조회 end ---");
        System.out.println();
    }
```

🔍 출력물 확인

```java
--- Header 편의 조회 start ---
[Host 편의 조회]
request.getServerName() = localhost
request.getServerPort() = 8080

[Accept-Language 편의 조회]
locale = ko_KR
locale = ko
request.getLocale() = ko_KR

[cookie 편의 조회]
Idea-59ed549d: e9f7163f-b84b-422b-b9e0-2e7da3360da5

[Content 편의 조회]
request.getContentType() = null  // get 으로 요청했기 때문에 content type 이 없다.
request.getContentLength() = -1
request.getCharacterEncoding() = UTF-8
--- Header 편의 조회 end ---
```