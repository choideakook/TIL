# V5 - 유연한 Controller 1

## ✏️ V4 의 문제점

- V4 의 방식은 매우 편리하지만 경우에 따라서 V1 이나 V2, V3 같은 방식이 필요할 수 도 있다.
    - V4 방식을 사용하면 url 매핑을 위한 Map 의 value 값이 정해저있기 때문에 V4 방식만 사용할 수 있기 때문에 이러한 문제를 해결할 수 없다.
    - 이러한 문제를 해결하기 위해서 `어댑터 패턴` 을 사용하 해결할 수 있다.

<br>

## ✏️ V5 적용

### 📍 V5 의 첫번째 목표

- V3 의 컨트롤러 어댑터를 생성해 V5 로 오는 요청 messager 를 어댑터를 연결해 V3 로 실행시킨다.

### 📍 V5 의 구조

- ***핸들러 어댑터*** : 중간에 어댑터 역할을 하는 객체 덕분에 다양한 종류의 Handler (Controller) 를 호출할 수 있다.
- ***핸들러*** : Controller 를 더 넓은 범위인 핸들러로 명명했다.
    - 어댑터가 있기 때문에 꼭 Controller 의 개념 뿐 아니라 어떤 것이든 해당하는 종류의 어댑터만 있다면 다 처리할 수 있기 때문이다.

<img width="531" alt="s8561" src="https://user-images.githubusercontent.com/115536240/219230845-86dcfae6-8328-450f-9dbc-7a8e105703fd.png">

<br>

### 📍 My Handler Adapter - interface

- 다양한 어댑터를 사용하고 객체지향적으로 관리하기 위해 interface 를 생성한다.

```java
package com.example.servlet.web.frontcontroller.v5;

import com.example.servlet.web.frontcontroller.ModelView;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public interface MyHandlerAdapter {

    // 요청받은 Controller 를 처리할 수 있는지 판별하는 기능
    boolean supports(Object handler);

    // 검증이 끝난 요청을 Handler 와 연결해 실행하는 기능
    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}
```

<br>

### 📍 Controller V3 Handler Adapter

- 요청을 Handler 에 연결시켜줄 adapter 중 Controller V3 로 연결 시킬 adapter 이다.
- V3 로 처리할 수 있는지 여부를 판별한다.
- 판별된 요청을 Handler 에 연결해 처리하고 Model View 객체를 반환한다.

```java
package com.example.servlet.web.frontcontroller.v5.adapter;

import com.example.servlet.web.frontcontroller.ModelView;
import com.example.servlet.web.frontcontroller.v3.ControllerV3;
import com.example.servlet.web.frontcontroller.v5.MyHandlerAdapter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ControllerV3HandlerAdapter implements MyHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        //요청한 구현체가 V3 를 상속하는지 확인하는 로직
        return (handler instanceof ControllerV3);
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {

        //Boolean 으로 판별이 된 Object 로 로직을 수행한다.
        // Controller V3 로 type 을 캐스팅 한다.
        ControllerV3 controller = (ControllerV3) handler;

        // 요청 parameter 를 map 으로 변환
        Map<String, String> paramMap = createParamMap(request);
        // 변환된 map 을 통해 Controller 를 실행시킨다.
        // Controller 의 business 로직의 결과와 논리 이름을 return 값으로 받는다.
        ModelView mv = controller.process(paramMap);
        //반환된 return 값을 반환한다.
        return mv;
    }

    //-- 요쳥 param 을 map 으로 변환 하는 method --//
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

### 📍 Front Controller Servlet V5

- 요청 URL 을  key 값으로 Handler 를 찾고, 구현체 (Handler) 에 맞는 interface 를 찾아서 adapter 에 연결시킨다.
    - 연결된 adapter 는 Handler 를 실행시킴
- Handler 의 business 로직이 완료된 return 값을 사용해 view 를 생성하고 호출한다.
    - view 는 jsp 를 호출하고 html 코드를 렌더링함

```java
package com.example.servlet.web.frontcontroller.v5;

import com.example.servlet.web.frontcontroller.ModelView;
import com.example.servlet.web.frontcontroller.MyView;
import com.example.servlet.web.frontcontroller.v3.ControllerV3;
import com.example.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
import com.example.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
import com.example.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;
import com.example.servlet.web.frontcontroller.v5.adapter.ControllerV3HandlerAdapter;
import org.apache.coyote.Adapter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
public class FrontControllerServletV5 extends HttpServlet {

    // 기존의 매핑을 위한 Map 의 value 값은 특정 Controller 의 interface 였지만,
    // handler 의 value 값은 무엇이든 담을 수 있는 Object 타입이다.
    private final Map<String, Object> handlerMappingMap = new HashMap<>();
    // 정해진 Handler 의 부모 interface 를 보관하는 List
    private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();

    //-- 생성자를 통해 구현체와 interface 목록을 미리 세팅한다. --//
    public FrontControllerServletV5() {
        // 요청 url 을 key 값으로 Handler 구현체를 찾기위한 로직
        initHandlerMappingMap();
        // Handler 의 부모 interface 를 찾는 로직
        initHandlerAdapters();
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // 요청 URL 을  handler Mapping Map 으로 매핑할 수 있는 Controller (handler) 를 생성해줌
        // 
        Object handler = getHandler(request);
        // mapping 할 수 있는 url 이 없을경우 404 not found 출력
        if (handler == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // 획득한 handler 가 상속하고있는 interface 를 찾는 로직
        MyHandlerAdapter adapter = getHandlerAdapter(handler);

        // 획득한 interface 와 구현 controller (handler) 를 실행한다.
        // business logic 을 완료하고 return 값인 model view 를 반환한다.
        ModelView mv = adapter.handle(request, response, handler);

        // model view 에서 논리 이름을 추출해 리졸버를 통해 완전한 경로로 완성해줌
        String viewName = mv.getViewName();
        MyView view = viewResolver(viewName);

        // JSP 파일을 호출해 HTML 을 랜더링한다.
        view.render(mv.getModel(), request, response);
    }

    // url 에 맞는 Controller 를 map 에 보관
    private void initHandlerMappingMap() {
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
    }

    // Controller 에 맞는 어댑터를 List 에 보관
    private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter());
    }

    // 요청 URL 을  handler Mapping Map 으로 매핑할 수 있는 Controller 를 반환하는 method
    private Object getHandler(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
        Object handler = handlerMappingMap.get(requestURI);
        return handler;
    }

    private MyHandlerAdapter getHandlerAdapter(Object handler) {
        MyHandlerAdapter handlerAdapter;
        for (MyHandlerAdapter adapter : handlerAdapters) {
            if (adapter.supports(handler)) {
                handlerAdapter = adapter;
                return adapter;
            }
        }
        throw new IllegalArgumentException("handler adapter 를 찾을 수 없습니다.");
    }

    private static MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }
}
```

<br>
