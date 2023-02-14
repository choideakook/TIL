# HttpServletResponse 기본 사용법

## ✏️ HttpServletResponse

### 📍 역할

[🔗 HTTP Message](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/2%20HTTP%20개념과%20메서드/230120%201%20모든것이%20HTTP.md)

- HTTP 응답 메시지 생성
    - HTTP 응답 코드 지정
    - 헤더 생성
    - 바디 생성
- 편의 기능 제공
    - Content-type 을 편리하게 작성하는 기능 제공
    - 쿠키를 객체로 편리하게 넣을 수 있는 기능 제공
    - Redirect

[🔗 300 Redirect](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%202%203XX%20Redirction.md)

<br>

### 📍 Servlet Class 생성

- HTTP 응답 메시지 작성
    
    [🔗 HTTP 상태 코드](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%201%20HTTP%20상태%20코드.md)
    
    [🔗 캐시 무효화](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/5%20HTTP%20헤더%20-%20캐시와%20조건부%20요청/230126%204%20캐시%20무효화.md)
    

```java
package com.example.servlet.basic.response;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name = "responseHeaderServlet", urlPatterns = "/response-header")
public class ResponseHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //--- status line ---//
        // HTTP 응답 코드를 넣을 수 있음
        // parameter 에 코드 숫자 (200) 을 직접 넣어도 가능하지만
        // 되도록 상수로 지정된 변수를 사용하는것이 좋다.
        response.setStatus(HttpServletResponse.SC_OK);

        //--- header ---//
        response.setHeader("Content-Type", "text/plain;charset=utf-8");
        // 케시 설정 (무효화하는 설정을 했음)
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        // 과거 버전인 HTTP 0.1 까지 모든 케시를 무효화함
        response.setHeader("Pragma", "no-cache");
        // 내가 원하는 임의의 header 정보도 작성할 수 있음
        response.setHeader("my-header", "hello");

        PrintWriter writer = response.getWriter();
        writer.println("Ok");
    }
}
```

🔍 web URL 에 접속해 개발자 모드로 확인해보면 내가 작성한 요청 message 를 확인할 수 있다.

<img width="550" alt="s8281" src="https://user-images.githubusercontent.com/115536240/218615761-74c77278-a3a3-4388-a3cf-117edd38a59d.png">

<br>

### ⚠️ 만약 status 를 Bad Request 400 을 넣을 경우

```java
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setStatus(HttpServletResponse.SC_BAD_REQUEST);

        ...
```

상태코드가 400 으로 바뀐다.

<img width="550" alt="s8282" src="https://user-images.githubusercontent.com/115536240/218615682-28155ab9-3c8c-4a1f-b5af-a81155484bd3.png">

<br>

## ✏️ Response 의 다양한 편의 Method

```java
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ...

        //-- content 관련 편의 method --//
//        content(response);

        //-- 쿠키 관련 편의 method --//
        cookie(response);

        //-- redirect 편의 method --//
        redirect(response);

        PrintWriter writer = response.getWriter();
        writer.println("Ok");
    }

    private void content(HttpServletResponse response) {
        // 방법 1.
        // Content-Type: text/plain;charset=utf-8
        // 방법 2.
        //response.setHeader("Content-Type", "text/plain;charset=utf-8");
        // 방법 3.
        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");

        // content-length 생략시 자동 생성
        // 직접 작성할경우 작성된 값이 출력됨
        // response.setContentLength(2);
    }

    private void cookie(HttpServletResponse response) {
        // 방법 1.
        // Set-Cookie: myCookie=good; Max-Age=600;
        // 방법 2.
        // response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
        // 방법 3.
        Cookie cookie = new Cookie("myCookie", "good");
        cookie.setMaxAge(600); //600초 (생명주기 설정)
        response.addCookie(cookie);
    }

    private void redirect(HttpServletResponse response) throws IOException {
        //-- 목표 --//
        // Status Code 302
        // Location: /basic/hello-form.html

        // 방법 1.
//        response.setStatus(HttpServletResponse.SC_FOUND); //302
        // 리다이렉트로 인해 해당 url 로 자동으로 이동한다.
//        response.setHeader("Location", "/basic/hello-form.html");
        // 방법 2.
        response.sendRedirect("/basic/hello-form.html");
    }
```
