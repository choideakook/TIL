# 221221 Controller 구현

## ✏️ HomeController

- welcome page url 을 받아옴

```java
package login.loginspring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

    @GetMapping ("/")
    public String home(){
        return "home";
    }
}
```

### 🔍home.html

- h1
- label id / text field / 로그인 button
- 회원가입 button

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">

<body>

<div class="container">
        <div>
            <h1>Log in service</h1>
            <form action="/member/login" method="post">
                <div class="form-group">
                    <label for="userid">ID</label>
                    <input type="text" id="userid" name="userid" placeholder="ID를 입력하세요">
                </div>
                <button type="submit">Log in</button>
            </form>
            <form action="/member/create" method="post">
                <button type="submit">Join</button>
            </form>
        </div>
</div>  <!-- /container -->
</body>
</html>
```

## ✏️ MemberController

- wellcome page 의 버튼을 past 방식으로 받아옴
- 일단 아무 기능 없이 잘 작동되는지만 테스트 해봤음

```java
package login.loginspring.controller;

import login.loginspring.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;

@Controller
public class MemberController {

    private final MemberService service;

    @Autowired
    public MemberController(MemberService service) {
        this.service = service;
    }

    @PostMapping ("/member/login")
    public String loginForm (){
        return "/member/loginForm";
    }

    @PostMapping ("/member/create")
    public String createForm (){
        return "/member/createForm";
    }
}
```

### 🔍members/loginForm.html

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">

<body>
    <div class="container">
        <div>
            <h1>Create Form</h1>

        </div>
    </div>  <!-- /container -->
</body>
</html>
```

### 🔍members/createForm.html

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">

<body>

<div class="container">
    <div>
        <h1>Log in Form</h1>

    </div>
</div>  <!-- /container -->
</body>
</html>
```

### ❗️ error 발생

[THYMELEAF][http-nio-8080-exec-2] Exception processing template "/members/createForm": Error resolving template [/members/createForm], template might not exist or might not be accessible by any of the configured Template Resolvers

→ mapping 한 url 을 html 파일로 return 하는 과정에서 경로나 파일명이 잘못 입력되있을 때 발생하는 오류

- 💡 해결방법 : ”” 안의 경로를 확인하자