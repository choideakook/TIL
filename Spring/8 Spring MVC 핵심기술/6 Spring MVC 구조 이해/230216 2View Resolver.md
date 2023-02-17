# View Resolver

## ✏️ Web Servlet Controller 의 View Resolver

지난시간에 사용했던 Spring MVC 사용 이전의 Servlet 프래임워크를 이용해 View Resolver 의 로직을 알아보자.

### 📍 JSP 파일인 new-form.jsp 를 랜더링 하기

지난번에 만든 `OldController` 를 실행시키면 `new-form.jsp` 가 페이지를 랜더링 하는것이 목표이다.

- return 값에 Model View 를 생성하고 jsp 파일의 논리 이름을 입력했다.

```java
package com.example.servlet.web.springmvc.old;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component("/springmvc/old-controller")
public class OldController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {

        System.out.println("OldController.handleRequest");
        return new ModelAndView("new-form");
    }
}
```

🔍 출력물

- 시스템 콘솔창의 출력은 정상석으로 되었지만 web page 에서 html 파일은 랜더링 되지 않았다.
    - Spring 은 JSP 파일의 경로를 찾을 수 있는 방법이 없어 404 에러가 발생했다.
    - 문제를 해결하기 위해서 별도로 View Resolver 를 만들어 주어야 한다.

<br>

### 📍 View Resolver

- application.properties 에서 view resolver 환경설정을 할 수 있다.
    - 논리 이름 앞의 경로와, 뒤의 확장자를 입력하면 spring 이 직접 완전한 경로로 완성해준다.

```java
logging.level.org.apache.coyote.http11=debug

//-- view reslover --/
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```

- 다시 url 로 접속해보면 정상적으로 html 이 랜더링 된다.