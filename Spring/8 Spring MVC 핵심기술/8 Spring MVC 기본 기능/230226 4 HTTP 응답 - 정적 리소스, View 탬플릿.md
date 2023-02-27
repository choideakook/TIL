# HTTP 응답 - 정적 리소스, View 탬플릿

## ✏️ 응답을 만드는 3가지 방법

1. ***정적 리소스***
    - 웹 브라우저에 정적인 HTM, CSS, JS 를 제공할 때
2. ***View 탬플릿 사용***
    - 웹 브라우저에 동적인 HTML 을 제공할 때
    - SSR
3. ***HTTP 메시지 사용***
    - HTTP API 를 제공하는 경우
    - HTTP message body 에 JSON 같은 형식으로 data 를 실어 보낸다.

<br>

## ✏️ 정적 리소스 응답방법

- 별도의 Mapping method 없이 단순히 resource 의 파일 경로만 url 에 입력 하는 것 만으로도 해당 위치의 파일이 응답 된다.
    - 별도 매핑 Method 필요 없음
    - url 에 html 파일의 경로를 입력해 요청
    - 자동으로 해당 url 의 파일이 응답

### 📍 정적 리소스 패키지

- Spring Boot 는 클래스 패스의 다음 디렉토리에 있는 정적 리소스를 제공한다.
    - static, public, resources, META-INF/resources
    - 해당 폴더에 있는 파일은 정적 리소스로 인식한다.
        - html, img, mp4 …

### 📍 정적 리소스 만들어 보기

- text 박스가 있는 간단한 정적 html 파일이다.
    - 파일이 위치한 (/static)/basic/hello.html 만 url 에 입력해도 별도 method 없이 작동된다.
        - http://localhost:8080/basic/hello.html

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

<br>

## ✏️ View 탬플릿

- View 탬플릿을 거처서 HTML 이 생성되고, View 가 응답을 만들어서 전달한다.
- 일반적으로 HTML 을 동적으로 생성하는 용도로 사용하지만,
View 템플릿이 만들 수 있는 것이라면 뭐든지 가능하다.

### 📍 View 탬플릿 패키지

- Spring Boot 는 templates 패키지내의 파일을 View 탬플릿 파일로 인식한다.

### 📍 Template - HTML 파일 준비하기

- 별도의 매핑 method 를 통해서 요청값을 받아서 html 에 view 를 통해 전달해 파일을 완성시켜서 응답해야 한다.

```html
<!DOCTYPE html>
	<!-- 타임리프를 사용하기 위한 기본 세팅 -->
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
	<!-- View 에서 넘겨받은 key 값이 출력된다. -->
	<!-- 만약 넘겨받은 값이 없다면 empty 가 출력됨 -->
<p th:text="${data}">empty</p>
</body>
</html>
```

<br>

### 📍 V1 - ***매핑 method 생성***

- ModelAndView 를 만들어 원하는 값을 Model 를 통해 HTML 파일에 전달해 동적으로 만들어 주었다.
    
    [🔗 ModelAndView](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/7%20Spring%20MVC%20적용/230216%201%20Spring%20MVC%20-%20V1%20시작하기.md)
    

```java
package hello.springmvc2.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {

        // ModelAndView 생성 
        // 경로 - response/hello
        // key - data / value - hello!
        ModelAndView mav = new ModelAndView("response/hello")
                .addObject("data", "hello!");

        return mav;
    }
}
```

<br>

### 📍 V2 - View Path 를 논리이름으로 반환하기

- Model 을 따로 분리해 addAttribute 를 사용해 Model 을 통해 HTML 에 data 를 전달했다.
- View Path 는 String 형태의 논리이름으로 Dispatcher Servlet 에 반환했다.

```java
    //-- 실무에서 많이 사용되는 방식 --//
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {

        model.addAttribute("data", "hello!");

        return "response/hello";
    }
```

### ⚠️ 실수로 @ResponseBody 어노테이션을 붙일경우

[🔗 @ResponseBody](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230226%202%20HTTP%20요청%20-%20단순%20text.md)

- 반환값을 논리이름으로 인식하지 않고 단순 Text 로 인식해버린다.
    - HTML 파일이 응답 되지 않고,
    단순 Text 로 String 이 응답되어 버린다.
    - @RestController 어노테이션도 마찬가지 이다.

<br>

### 📍 V3 - 반환값 생략

- 반환값을 void 로 변경하고,
맵핑 url 과 html url 의 경로를 맞춰주면 반환값 없이도 Spring Boot 가 자동으로 html 을 찾아 응답 해준다.
    - @Controller 를 사용하고,
    HTTP message body 를 처리하는 parameter 가 없어야 제대로 작동된다.

```java
    @RequestMapping("/response/hello")
    public void responseViewV3(Model model) {

        model.addAttribute("data", "hello!");
    }
```

⚠️ ***실무에서는 이 방식을 잘 사용하지 않는다.***

- 명시성이 떨어지고, mapping url 과 디렉토리가 잘 맞아떨어지지 않는다.
