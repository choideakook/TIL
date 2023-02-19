# HTTP 요청 V1 - Param Query Param, HTML Form

[🔗 HTTP 요청의 3가지 방법](https://github.com/choideakook/TIL/tree/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답)

[🔗 HTTP 요청 - Query Param 방식](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%201%20GET%20Query%20Parameter.md)

[🔗 HTTP 요청 - HTML Form 방식](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%202%20POST%20HTML%20Form.md)

<br>

## ✏️ Query Param, HTML Form

- `HttpServletRequest` 의 `getParameter()` 를 사용해 요청 param 값을 조회할 수 있다.
    - URL 의 param 문법과 message body 의 param 문법이 같아서 같은 방법으로 조회 가능

### 📍 GET - Query Parameter 전송

- Query Parameter 방식의 GET 요청을 받는 로직
- 정상적으로 작동한다.

```java
package hello.springmvc.basic.request;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Slf4j
@Controller
public class RequestParamController {

    // 어떤 method 도 받을 수 있도록 request mapping 으로 생성
    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username = {}, age = {}", username, age);

        response.getWriter().write("ok");
    }
}
```

<br>

### 📍 POS - HTML Form 전송

- 클라이언트의 요청을 입력할 수 있는 form 을 만들어 요청을 message body 에 담아 전송한다.
    - 방금 만든 requestParamV1 로 요청을 보내도록 만들었다.
    - Query Param 의 문법과 Message Body 의 문법이 같기 때문에 하나의 method 로 사용 가능
- 정상적으로 작동한다.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/request-param-v1" method="post">
        username: <input type="text" name="username" />
        age: <input type="text" name="age" />
        <button type="submit">전송</button>
    </form>
</body>
</html>
```