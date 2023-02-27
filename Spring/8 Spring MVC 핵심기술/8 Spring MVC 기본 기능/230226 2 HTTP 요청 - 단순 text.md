# HTTP 요청 - 단순 text

## ✏️ Message Body 에 요청 방식

- HTTP Message body 에 데이터를 직접 담아서 요청하는 방식이다.
    - HTTP API 에서는 JSON 을 주고받으며 사용된다.
    - JSON, XML, TEXT …
    - POST, PUT, PATCH
- 요청 Paramter 방식과 다르게 Body 에 직접 data 가 넘어오면
`@RequestParam` , `@ModelAtrribute` 를 사용할 수 없다.
    - HTML Form 형식으로 전달되는 경우는 Paramter 로 인정되기 때문

<br>

## ✏️ V1 - Text 요청 매핑하기

- 요청 message body 가 단순 text 일경우엔 stream 형식으로 매핑해서 사용해야 한다.
    - stream 으로 가져온 text 는 bite code 형식이기 때문에 꼭 유니코드를 설정해 언어를 명시해 주어야 한다.

```java
package hello.springmvc2.basic.requestmapping;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;

import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;

@Slf4j
@Controller
public class RequestBodyStringController {

    @PostMapping("/request-body-string-v1")
    public void requestBodySting(HttpServletRequest request, HttpServletResponse response) throws IOException {
        // stream 으로 data 를 get 하는 방식
        ServletInputStream inputStream = request.getInputStream();
        // stream 은 bite code 로 되어있기 때문에
        // stream 을 사용할 땐 반드시 어떤 문자로 출력할지 명시해주어야 한다.
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        log.info("messageBody = {}", messageBody);
        response.getWriter().write("ok");
    }
}
```

<br>

## ✏️ V2 - InputStream , Writer

- request 와 response 를 사용하는 것이 아닌 필요한 기능을 param 으로 지정하면 더 간결하게 로직을 만들어 낼 수 있다.
    - 매개변수로 InputStream 과 Writer 를 받아서 사용한다.

```java
    @PostMapping("/request-body-string-v2")
    public void requestBodyStingV2(InputStream inputStream, Writer responseWriter) throws IOException {

        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        log.info("messageBody = {}", messageBody);
        responseWriter.write("ok");
    }
```

<br>

## ✏️ V3 - HttpEntity
HTTP Message Converter

- ***Http Entity 사용***
    - HTTP header 와 body 정보를 편리하게 조회할 수 있는 객체
    - ⚠️ Parameter 를 조회하는 기능과는 관계 없다.
- ***Http Entity 는 응답에도 사용 가능하다.***
    - 응답 message header 와 body 정보도 직접 반환 가능
    - ⚠️ 당연히 view 조회는 할 수 없다.
- V2 에서 사용한 Stream 변환작업을 매개변수 단계에서 해결해 별도의 로직을 만들지 않아도 된다.
- 반환값을 HttpEntity 로 설정해 응답 message 도 별도 매개변수 없이 해결할 수 있다.

```java
    @PostMapping("/request-body-string-v3")
    public HttpEntity<String> requestBodyStingV3(HttpEntity<String> httpEntity) throws IOException {

        String messageBody = httpEntity.getBody();

        log.info("messageBody = {}", messageBody);
        return new HttpEntity<>("ok");
    }
```

<br>

### 📍RequestEntity , ResponseEntity

- HttpEntity 의 request 기능과 response 기능을 나누어서 사용하는 것도 가능하다.
    - 두 객체는 HttpEntity 를 상속받은 객체이다.
- ***RequestEntity***
    - 요청 message 를 조회할 수 있음
    - 매개 변수로 입력해 주어야 함
- ***ResponseEntity***
    - 응답 message 를 작성할 수 있음
    - message 뿐 아니라 상태코드도 설정할 수 있음
        - HttpEntity 에서도 가능하다.
    - 매개변수 설정 없이 사용가능하다.

```java
    @PostMapping("/request-body-string-v3")
    public HttpEntity<String> requestBodyStingV3(RequestEntity<String> httpEntity) throws IOException {

        String messageBody = httpEntity.getBody();

        log.info("messageBody = {}", messageBody);
        // 상태코드를 200 OK 가 아닌
        // 201 Create 로 설정했음
        return new ResponseEntity<String>("ok", HttpStatus.CREATED);
    }
```

<br>

## ✏️ V4 - @RequestBody , @ResponseBody

- ***@RequestBody***
    - V1 부터 V3 에 걸처 개선된 부분을 모두 포함하고,
    심지어 매개변수에서 객체상태 까지로 만들어줘 get 작업을 할 필요도 없어진다.
    - 지금까지 method 에 선언되어 있던 throws 도 해결해 준다.
- ***@ResponseBody***
    - 반환값을 별도의 라이브러리 없이 String 으로 returen 할수 있게 만들어 준다.
    - @RestController 어노테이션을 Class 단위로 선언하는 것으로 대체가 가능하다.
        - 상태코드는 반환할 수 없음
- ⚠️ `@RequestParam` 과 `@ModelAttribute` 와는 관계 없는 기술이다.

```java
    //-- 실무에서 많이 사용되는 방식 --//
    @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStingV4(@RequestBody String messageBody){

        log.info("messageBody = {}", messageBody);
        return "ok";
    }
```

### ⚠️ V4 의 한계

- 요청 message 의 header 를 조회할 수 없다.
- header 가 필요할 경우 매개변수에 @ReqestHeader 를 사용해 해결할 수 있다.

[🔗 **API 요청 Mapping 방법**](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230217%202%20HTTP%20요청.md)