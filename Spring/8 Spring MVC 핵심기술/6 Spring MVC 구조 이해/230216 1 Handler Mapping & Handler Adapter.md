# Handler Mapping & Handler Adapter

### ⚠️ Spring MVC 를 직접 이해하는 것은 매우 복잡하고 어렵기 때문에

과거에 Spring 이 사용했던 비교적 간단한 Controller 로 Handler Mapping 과 adapter 를 확인해보자.

<br>

## ✏️ Controller Interface

⚠️ 과거의 Spring 버전

- Return 값으로 Model View 를 반환한다.
- request 와 response 를 직접 Controller 에 전달한다.

```java
public interface Controller {
    ModelAndView handleRequest(
        HttpServletRequest request, 
        HttpServletResponse response ) throws Exception;
}
```

<br>

### 📍 직접 사용해 보기

1. Spring Bean 등록
    - @Component 의 기능으로 `/springmvc/old-controller` 의 이름으로 Spring Bean 에 등록된다.
2. 요청 HTTP message 접수
    - `DispatcherServlet` 는 모든 HTTP message 를 매핑해 알맞은 `Handlr` 를 찾기위해 `HandlerMapping` 에 URL 을 확인한다.
3. URL 매핑
    - `HandlerMapping` 객체는 요청 URL 을 Spring Bean 에서 찾는다.
    - 이미 요청 URL 과 Spring Bean 에 등록한 객체 이름을 통일했기 때문에 @Component 로 등록한 `/springmvc/old-controller` 가 매핑된다.
4. Handler 실행
    - Handler 를 매핑한 후 `HandlerAdapter` 가 Handler 가 상속하고있는 interface 어댑터를 찾아준다.
    - 이렇게 `Adapter` 를 찾아 `Handler` 를 실행 시키고 결과값 `ModelView` 를 반환한다.

```java
package com.example.servlet.web.springmvc.old;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

// @Component 로 Spring Bean 등록
// 이렇게 등록된 Bean 의 이름과 동일하게 urlPatterns 이 등록된다.
@Component("/springmvc/old-controller")
public class OldController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {

        System.out.println("OldController.handleRequest");
        return null;
    }
}
```

### 🔍 실행 결과

url 을 입력하고 요청 http 를 보내니 "OldController.handleRequest" 가 정상적으로 출력되었다.