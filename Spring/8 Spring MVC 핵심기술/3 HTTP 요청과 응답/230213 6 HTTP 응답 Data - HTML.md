# HTTP 응답 Data - HTML

## ✏️ 메시지 응답의 3가지 방법

1. 단순 Text 응답
    - `writer*.println("Ok");`
2. HTML 응답
3. HTTP API - Message body JSON 응답

<br>

## ✏️ HTML 응답 방법

```java
package com.example.servlet.basic.response;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // Content-type: text/html;charset=utf-8
        // html 파일을전송하기 위해 content-type 에 html 을 입력
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        // message body
        // 직접 html 코드를 출력에 작성한다.
        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("    <div>안녕?</div>");
        writer.println("</body>");
        writer.println("</html>");

    }
}
```

🔍 결과물 확인

- message body 에는 HTML 코드가 적혀있지만 web 에서는 text 가 잘 출력되었다.

<img width="400" alt="s8291" src="https://user-images.githubusercontent.com/115536240/218615867-bb836d69-54b7-49d2-9115-82b3b0b234d5.png">

<br>

응답 massage header

<img width="400" alt="s8292" src="https://user-images.githubusercontent.com/115536240/218615873-5986f7da-6202-4d88-80de-cc8d45f603dc.png">

<br>

## ✏️ API JSON 응답 방법
Rest API

```java
package com.example.servlet.basic.response;

import com.example.servlet.basic.HelloData;
import com.fasterxml.jackson.databind.ObjectMapper;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {

    // Json 형식으로 변환하기 위한 객체 생성
    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Content-type: application/json
        // json 은 스팩상 utf-8 로 정의 되었기 때문에
        // 별도로 charset=utf-8 은 사실 의미 없는 param 이다.
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        // 요청값을 json 으로 변환시키기 위해 임의로 요청값을 set
        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);

        // hello date 의 값을 Json 형식으로 바꾼다.
        // {"username":"kim", "age": 20}
        String result = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(result);
    }
}
```

<br>

🔍 웹에서 확인

Json 형식으로 잘 응답 되었다.

<img width="700" alt="s82101" src="https://user-images.githubusercontent.com/115536240/218615876-71dc20e9-c747-4dc1-9bf9-67f0128b8a0a.png">
