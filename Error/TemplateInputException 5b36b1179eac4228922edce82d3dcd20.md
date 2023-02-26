# TemplateInputException

Template / Input / Exception

탬플릿 / 입력 / 예외

직역해보면 템플릿 관련해서 입력을 잘못해 발생된 예외임을 예상할 수 있다.

## ✏️ 발단

- Spring MVC 동적 View Template 기초 강의를 듣던도중 예외가 발생했다.

```html
TemplateInputException: 
    Error resolving template [hello], 
    template might not exist or might not be accessible by any of the configured Template Resolvers
```

- root cause
    - message 를 살펴보면 탬플릿이 존재하지 않거나,
    탬플릿 리졸버에 접근할 수 없다고 하는것 같다.

```html
TemplateInputException: 
    Error resolving template [hello], 
    template might not exist or might not be accessible by any of the configured Template Resolvers
```

<br>

- 문제의 코드

```java
package hello.springmvc2.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {

        ModelAndView mav = new ModelAndView("hello").addObject("data", "hello!");

        return mav;
    }
}
```

## ✏️ 원인

콘솔 메시지가 제안한 첫번째 원인인 탬플릿이 존재하지 않는것 같다의 의미는
말 그대로 내가 말한 경로에서 “hello.html” 을 찾아봤는데 아무것도 못찾았다는 의미이다.

<br>

확인해보니 template/response/hello.html 의 경로에 파일이 있엇다.

## ✏️ 해결

ModelAndView 의 경로를 "response/hello" 로 바꾸니 문제가 해결되었다.

```java
package hello.springmvc2.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {

        ModelAndView mav = new ModelAndView("response/hello").addObject("data", "hello!");

        return mav;
    }
}
```