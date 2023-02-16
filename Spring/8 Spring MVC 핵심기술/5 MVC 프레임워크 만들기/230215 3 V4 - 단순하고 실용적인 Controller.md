# V4 - 단순하고 실용적인 Controller

## ✏️ V3 의 문제점

- V3 에서는 서블릿의 종속성을 제거하고 뷰 경로의 중복을 제거해 설개적인 개선을 이루었다.
- 문제점
    - 실제 Controller 를 구현할 때 항상 ModelView 객체를 반환해야 하는 부분에서 중복이 발생된다.
    - Front Controller 가 Model 까지 완성하는 방식으로 문제를 개선할 수 있다.

<br>

## ✏️ V4 적용

### 📍 V4 의 구조

- 구조는 V3 와 동일하지만 Controller 에서 `ModelView` 를 반환하지 않고 `ViewName` 을 반환한다.

<img width="536" alt="s8551" src="https://user-images.githubusercontent.com/115536240/219230732-de1b1b66-61d6-4359-b822-67f1950269ca.png">

<br>

### 📍 ControllerV4 - intrerface

- 단순히 view 의 논리 이름만 반환시키기 위해 반환값을 Stirng 으로 생성한다.
- Map model 을 생성해 결과값을 받아서 Front Controller 에서 관리할 수 있게 만든다.

```java
package com.example.servlet.web.frontcontroller.v4;

import java.util.Map;

public interface ControllerV4 {

    String process(Map<String, String> paramMap, Map<String, Object> model);
}
```

<br>

### 📍 회원 등록 폼

- 단순히 view 의 논리이름을 return 하면 된다.

```java
package com.example.servlet.web.frontcontroller.v4.controller;

import com.example.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.Map;

public class MemberFormControllerV4 implements ControllerV4 {

    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        
        //-- view path return --//
        return "new-form";
    }
}
```

<br>

### 📍 회원 저장 완료 페이지

- business logic 의 결과를 Map model 에 담고, 단순히 논리 이름을 return 한다.

```java
package com.example.servlet.web.frontcontroller.v4.controller;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import com.example.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.Map;

public class MemberSaveControllerV4 implements ControllerV4 {

    private MemberRepository repository = MemberRepository.getInstance();

    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {

        //-- business logic --//
        String username = paramMap.get("username");
        int age = Integer.parseInt(paramMap.get("age"));

        Member member = new Member(username, age);
        repository.save(member);

        //-- model 에 값을 put --//
        model.put("member", member);

        //-- View Path return --//
        return "save-result";
    }
}
```

<br>

### 📍 모든 회원 조회

```java
package com.example.servlet.web.frontcontroller.v4.controller;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import com.example.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.List;
import java.util.Map;

public class MemberListControllerV4 implements ControllerV4 {

    private MemberRepository repository = MemberRepository.getInstance();

    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {

        // business logic
        List<Member> members = repository.findAll();

        // model 에 값을 put
        model.put("members", members);

        // view path return
        return "members";
    }
}
```

<br>

### 📍 Front Controller V4

- Controller 의 결과를 관리하기 위해 Front Controller 에서 Map model 을 생성했다.
    - controller 구현체에 Map model 을 파라미터 값으로 넘겨준다.
- Controller 의 반환값을 view resolver 를 통해 완전한 경로로 완성해 My View 에 담아준다.
- My View 에 model 값을 넣어 실행시킨다.

```java
package com.example.servlet.web.frontcontroller.v4;

import com.example.servlet.web.frontcontroller.MyView;
import com.example.servlet.web.frontcontroller.v4.controller.MemberFormControllerV4;
import com.example.servlet.web.frontcontroller.v4.controller.MemberListControllerV4;
import com.example.servlet.web.frontcontroller.v4.controller.MemberSaveControllerV4;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@WebServlet(name = "frontControllerServletV4", urlPatterns = "/front-controller/v4/*")
public class FrontControllerServletV4 extends HttpServlet {

    private Map<String, ControllerV4> controllerMap = new HashMap<>();

    public FrontControllerServletV4() {
        controllerMap.put("/front-controller/v4/members/new-form", new MemberFormControllerV4());
        controllerMap.put("/front-controller/v4/members/save", new MemberSaveControllerV4());
        controllerMap.put("/front-controller/v4/members", new MemberListControllerV4());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String requestURI = request.getRequestURI();
        ControllerV4 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // 요청 param 을 map 으로 변환
        Map<String, String> paramMap = createParamMap(request);
        // controller 의 결과값을 담는 map 생성
        Map<String, Object> model = new HashMap<>();

        // Controller 실행
        // 요청 param Map 과 결과 data 를 담을 Map 을 갖는다.
        // return 값으로 논리 이름이 반환된다.
        String viewName = controller.process(paramMap, model);

        // 논리이름을 완전한 경로로 완성
        MyView view = viewResolver(viewName);

        // JSP 실행과 model 값을 Model 객체에 저장한다.
        view.render(model, request, response);
    }

    private static MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private static Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                        .forEachRemaining(paramName ->
                                paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}
```

<br>

### 📍 MyView

- V3 에서 만든 로직을 그대로 사용해도 문제없이 작동된다.

```java
package com.example.servlet.web.frontcontroller;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

public class MyView {

    private String viewPath;

    //-- 생성자 --//
    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }
    //-- V2 ---//
    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    //-- V3, V4 --//
    public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        modelToRequestAttribute(model, request);

        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    //-- controller 의 data 를 model 에 저장하는 method --//
    private static void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
        model.forEach((key, value) -> request.setAttribute(key, value));
    }
}
```
