# HTTP ์์ฒญ V1 - Param Query Param, HTML Form

[๐ย HTTP ์์ฒญ์ 3๊ฐ์ง ๋ฐฉ๋ฒ](https://github.com/choideakook/TIL/tree/main/Spring/8%20Spring%20MVC%20ํต์ฌ๊ธฐ์ /3%20HTTP%20์์ฒญ๊ณผ%20์๋ต)

[๐ย HTTP ์์ฒญ - Query Param ๋ฐฉ์](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20ํต์ฌ๊ธฐ์ /3%20HTTP%20์์ฒญ๊ณผ%20์๋ต/230213%201%20GET%20Query%20Parameter.md)

[๐ย HTTP ์์ฒญ - HTML Form ๋ฐฉ์](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20ํต์ฌ๊ธฐ์ /3%20HTTP%20์์ฒญ๊ณผ%20์๋ต/230213%202%20POST%20HTML%20Form.md)

<br>

## โ๏ธย Query Param, HTML Form

- `HttpServletRequest` ์ `getParameter()` ๋ฅผ ์ฌ์ฉํด ์์ฒญ param ๊ฐ์ ์กฐํํ  ์ ์๋ค.
    - URL ์ param ๋ฌธ๋ฒ๊ณผ message body ์ param ๋ฌธ๋ฒ์ด ๊ฐ์์ ๊ฐ์ ๋ฐฉ๋ฒ์ผ๋ก ์กฐํ ๊ฐ๋ฅ

### ๐ย GET - Query Parameter ์ ์ก

- Query Parameter ๋ฐฉ์์ GET ์์ฒญ์ ๋ฐ๋ ๋ก์ง
- ์ ์์ ์ผ๋ก ์๋ํ๋ค.

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

    // ์ด๋ค method ๋ ๋ฐ์ ์ ์๋๋ก request mapping ์ผ๋ก ์์ฑ
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

### ๐ย POS - HTML Form ์ ์ก

- ํด๋ผ์ด์ธํธ์ ์์ฒญ์ ์๋ ฅํ  ์ ์๋ form ์ ๋ง๋ค์ด ์์ฒญ์ message body ์ ๋ด์ ์ ์กํ๋ค.
    - ๋ฐฉ๊ธ ๋ง๋  requestParamV1 ๋ก ์์ฒญ์ ๋ณด๋ด๋๋ก ๋ง๋ค์๋ค.
    - Query Param ์ ๋ฌธ๋ฒ๊ณผ Message Body ์ ๋ฌธ๋ฒ์ด ๊ฐ๊ธฐ ๋๋ฌธ์ ํ๋์ method ๋ก ์ฌ์ฉ ๊ฐ๋ฅ
- ์ ์์ ์ผ๋ก ์๋ํ๋ค.

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
        <button type="submit">์ ์ก</button>
    </form>
</body>
</html>
```