# HTTP 요청 - JSON

## ✏️ JSON 을 사용한 통신

- 주로 CSR 방식의 코딩을 할 때 사용되는 파일 형식
- message body 를 통해 정보를 주고받는 다는 점에서 단순 Text 방식과 비슷하지만
단순 data 전달용 객체를 통해 필드값을 매핑하고 JSON 을 이용해 통신하는 점이 다르다.

<br>

## ✏️ V1 - 요청 JSON 매핑하기

### 📍 전송용 객체 만들기

```java
package hello.springmvc2.basic;

import lombok.Data;

@Data
public class HelloData {

    private String username;
    private int age;
}
```

### 📍 요청 JSON 매핑

```java
package hello.springmvc2.basic.requestmapping;

import com.fasterxml.jackson.databind.ObjectMapper;
import hello.springmvc2.basic.HelloData;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;

import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;

/**
 * {"username":"hello", "age":20}
 * content-type: application/json
 */
@Slf4j
@Controller
public class RequestBodyJasonController {

    private ObjectMapper objectMapper = new ObjectMapper();

    @PostMapping("/request-body-json-v1")
    public void requestBodyJasonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {

        // message body 를 매핑하기 위한 stream
        ServletInputStream inputStream = request.getInputStream();
        // bite code 를 해석하기 위한 유니코드 설정
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        
        log.info("messageBody = {}", messageBody);

        // data 를 주고받기 위한 객체를 통해 json 으로 요청한 값을 필드값으로 매핑함
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username = {}, age = {}", helloData.getUsername(), helloData.getAge());

        response.getWriter().write("ok");
    }
}
```

<br>

## ✏️ V2 - @RequestBody, @ResponseBody

- 단순 Text 를 주고받을 때 사용했던 방식과 동일하다.

[🔗 단순 Text 매핑](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230226%202%20HTTP%20요청%20-%20단순%20text.md)

```java
    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJasonV2(@RequestBody String messageBody) throws IOException {

        log.info("messageBody = {}", messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username = {}, age = {}", helloData.getUsername(), helloData.getAge());

        return "ok";
    }
```

<br>

## ✏️ V3 - data 전달 객체 바로 매핑하기

- message body 를 매핑해 data 전달용 객체로 변환할 필요 없이 매개변수 값에 바로 객체를 넣어서 사용할 수 있다.
    - 즉, @RequestBody 에 직접 만든 객체를 사용할 수 있게 된다.
    - 그럼 `objectMapper` 를 사용하지 않아도 되고 `throws` 도 선언하지 않아도 된다.
- 단순 Text 에서 했던 것 처럼 `HttpEntity<>` 를 사용하는 것도 가능하다. (강의에서 V4)
    
    [🔗 HttpEntity<> 사용방법](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230226%202%20HTTP%20요청%20-%20단순%20text.md)
    

```java
    // 매개변수에 바로 data 전달 객체를 넣어 
    // 요청값을 get 으로 사용할 수 있는 형태로 만들었다.
    @ResponseBody
    @PostMapping("/request-body-json-v3")
    public String requestBodyJasonV3(@RequestBody HelloData helloData) {

        log.info("username = {}, age = {}", helloData.getUsername(), helloData.getAge());

        return "ok";
    }
```

### ⚠️ @RequestBody 는 그냥 생략할 수 없다.

- 지금 형태의 method 의 매개변수값에 어노테이션이 없으면 `@ModelAttribute` 로 인식하게 되어버린다.
    
    
- `@RequestBody` 를 생략하려면 Class 레벨에 `@RestController` 를 붙이는 방식으로 생략할 수 있다.

<br>

## ✏️ V5 - data 전달 객체 바로 return 하기

- `@ResponseBody` 의 기능으로 return 값을 String 으로 지정할 수 있었던 것 처럼,
객체도 return 으로 반환할 수 있다.

```java
    //-- 실무에서 많이 사용되는 방식 --//
    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJasonV5(@RequestBody HelloData helloData) {

        log.info("username = {}, age = {}", helloData.getUsername(), helloData.getAge());

        return helloData;
    }
```

<br>

🔍 출력물 확인

- `@RequestBody` 가JSON 을 객체로 변환했던 것 처럼 
객체도 `@ResponseBody` 가 JSON 으로 변환되어 출력된다.

![s8881.png](HTTP%20%E1%84%8B%E1%85%AD%E1%84%8E%E1%85%A5%E1%86%BC%20-%20JSON%2037f7b2a2b4a84d21a997505b5f23b7e4/s8881.png)
