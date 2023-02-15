# Front Controller V1

## ✏️ V1 의 목표

[🔗 MVC 패턴으로 만든 기존 코드](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/4%20서블릿%2C%20JSP%2C%20MVC%20패턴%20적용/230214%204%20MVC%20패턴%20-%20적용.md)

기존 코드를 최대한 유지하면서 Front Controller 를 도입하는 것

- 구조를 맞추어두고 점진적으로 리팩토링 할 예정
    - V1 에서는 MVC 패턴의 한계를 개선하는 것이 아닌 프로젝트의 구성을 개선하는 것이 중점이다.

<br>

### 📍 V1 의 구조

![s8521.png](Front%20Controller%20V1%204b17b5642c784735b8c4e9752e24a2dc/s8521.png)

<br>

## ✏️ Controller

### 📍 Controller V1

- Controller 를 인터페이스로 구현한다.
- Front Controller 는 interface 를 호출해 구현화 관계없이 로직의 일관성을 가져갈 수 있다.

```java
package com.example.servlet.web.frontcontroller.v1;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public interface ControllerV1 {

    // servlet 의 service method 와 동일한 모양으로 interface 객체 생성
    void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
    
    
}
```

<br>

### 📍 회원 등록 폼 Controller

[🔗 MVC 패턴을 사용한 회원관리 application](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/4%20서블릿%2C%20JSP%2C%20MVC%20패턴%20적용/230214%204%20MVC%20패턴%20-%20적용.md)

- V1 을 상속하는 것을 제외한 로직을 MVC 패턴과 동일하게 만든다.
    - view path 도 동일하게 설정한다.
    - jsp 의 post 요청을 상대경로로 코딩해서 재활용이 가능하다.

```java
package com.example.servlet.web.frontcontroller.v1.controller;

import com.example.servlet.web.frontcontroller.v1.ControllerV1;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MemberFormControllerV1 implements ControllerV1 {

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

<br>

### 📍 회원 저장 완료 페이지

- MVC 패턴과 동일한 코드 작성

```java
package com.example.servlet.web.frontcontroller.v1.controller;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import com.example.servlet.web.frontcontroller.v1.ControllerV1;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MemberSaveControllerV1 implements ControllerV1 {

    private MemberRepository repository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        Member member = new Member(username, age);
        repository.save(member);

        request.setAttribute("member", member);

        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

<br>

### 📍 모든 회원 조회

- MVC 패턴과 동일한 코드 작성

```java
package com.example.servlet.web.frontcontroller.v1.controller;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import com.example.servlet.web.frontcontroller.v1.ControllerV1;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

public class MemberListControllerV1 implements ControllerV1 {

    private MemberRepository repository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        List<Member> members = repository.findAll();

        request.setAttribute("members", members);

        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

<br>

### 📍 Front Controller

- http 요청의 모든 url 을 mapping 해 map 정보를 통해 알맞는 Controller 구현체를 실행시키는 방식
    - 사전에 모든 url 정보와 알맞는 Controller 를 map 에 입력시켜놓아야 한다.

```java
package com.example.servlet.web.frontcontroller.v1;

import com.example.servlet.web.frontcontroller.v1.controller.MemberFormControllerV1;
import com.example.servlet.web.frontcontroller.v1.controller.MemberListControllerV1;
import com.example.servlet.web.frontcontroller.v1.controller.MemberSaveControllerV1;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

// url 경로에 * 을 입력하면 해당 경로로 요청되는 모든 url 을 매핑할 수 있다.
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {

    // Mapping 정보를 보관하는 Map 생성
    // key : url
    // value : Controller
    private Map<String, ControllerV1> controllerMap = new HashMap<>();

    // http 요청이 오면 url 을 map 에 put 해서 Controller 를 get 할 수 있다.
    public FrontControllerServletV1() {
        controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //-- business losic --//
        // front controller 가 제대로 작동하는지 확인하기 위한 출력 로직
        System.out.println("FrontControllerServletV1.service");

        // http 요청의 url 을 변수에 저장
        String requestURI = request.getRequestURI();
        // uri 를 통해 map 의 구현체를 변수에 저장
        ControllerV1 controller = controllerMap.get(requestURI);
        // 만약 해당 url 이 없을경우 404 not found 응답
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // map 에서 찾은 구현체를 실행시킨다.
        controller.process(request, response);
    }
}
```

<br>

## ✏️ 정리

- `@WebServlet` 의 Servlet 생성과 url 매핑 중복 문제를 해결했다.
- `forward` 중복이 해결되지 않았다.