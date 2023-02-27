# HTTP 응답 - API (body 에 직접 입력)

## ✏️ 단순 Text 응답 하기

- 자세한 내용은 HTTP 요청에서 다룬내용과 동일하다.
    
    🔗 HTTP 요청 - 단순 Text
    

```java
package hello.springmvc2.basic.response;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Slf4j
@Controller
public class ResponseBodyController {

    /**
     * 단순 Text
     */
    //-- HttpServlet 방식 --//
    @GetMapping("/response-body-string-v1")
    public void responseBodyV1(HttpServletResponse response) throws IOException {
        response.getWriter().write("ok");
    }

    //-- ResponseEntity 방식 --//
    @GetMapping("/response-body-string-v2")
    public ResponseEntity<String> responseBodyV2() throws IOException {
        return new ResponseEntity<>("ok", HttpStatus.OK);
    }

    //-- @ResponseBody 방식 --//
    @ResponseBody
    @GetMapping("/response-body-string-v3")
    public String responseBodyV3() {
        return "ok";
    }
}
```

<br>

## ✏️ JSON 응답 - Rest API

- 자세한 내용은 HTTP 요청에서 다룬내용과 동일하다.
    
    🔗 HTTP 요청 - JSON
    

```java
    /**
     * Rest API
     */
    //-- ResponseEntity 방식 --//
    @GetMapping("/response-body-json-v1")
    public ResponseEntity<HelloData> responseBodyJsonV1() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);

        return new ResponseEntity<>(helloData, HttpStatus.OK);
    }

    //-- @ResponseBody 방식 --//
    @ResponseBody
    @GetMapping("/response-body-json-v2")
    public HelloData responseBodyJsonV2() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);

        return helloData;
    }

    //-- @ResponseStatus 로 상태 코드 추가 --//
    @ResponseStatus (HttpStatus.OK)
    @ResponseBody
    @GetMapping("/response-body-json-v3")
    public HelloData responseBodyJsonV3() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);

        return helloData;
    }
```