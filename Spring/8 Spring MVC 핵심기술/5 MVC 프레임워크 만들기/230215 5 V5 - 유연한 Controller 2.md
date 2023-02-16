# V5 - 유연한 Controller 2

## ✏️ V5 의 두번째 목표

- 첫번째 목표인 V3 를 어댑터로 연결해 실행시켰듯이,
V4 의 Controller 도 어댑터를 생성해 연결해보자.

### 📍 고려해야할 문제

- V3 와 V4 의 반환 값이 달라 단순히 변수명만 바뀐 어댑터로는 `Front Controller` 의 로직이 작동하지않는다.
    - V3 는 `Controller` 의 `return` 값이 `Model view` 로 반환한다.
    - V4 는 `return` 값을 논리 이름인 `String` 으로 반환 한다.
- 새롭게 추가하는 `V4 의 어댑터` 내부에서
 반환하는 `return` 값을 V3 와 동일하게 맞춰주면 `Front Controller` 의 코드 수정 없이 작동이 가능하다.
    - OCP 의 원직을 지키며 새로운 어댑터를 추가할 수 있음

## ✏️ V5 - V4 어댑터 추가

### 📍 Front Controller Servlet V5

- V4 의 url 맵핑 정보와 CotrollerV4 인터페이스 정보를 추가한다.

```java
    // url 에 맞는 Controller 를 map 에 보관
    private void initHandlerMappingMap() {
        // V3
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
        // V4
        handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberSaveControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
    }
    // Controller 에 맞는 어댑터를 List 에 보관
    private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter()); // V3 어댑터
        handlerAdapters.add(new ControllerV4HandlerAdapter());// V4 어댑터
    }
```

<br>

### 📍 Controller V4 Handler Adapter

- Front Controller 의 스팩에 맞게 리턴값을 Model View 로 맞춰주어야 한다.
    - 새로운 Model view  객체를 생성하고 Controller 의 반환값인 논리 이름으로 필드를 채워준다.
    - business 로직 결과의 data 를 담은 Map model 을 Model view 필드에 set 해준다.

```java
package com.example.servlet.web.frontcontroller.v5.adapter;

import com.example.servlet.web.frontcontroller.ModelView;
import com.example.servlet.web.frontcontroller.v4.ControllerV4;
import com.example.servlet.web.frontcontroller.v5.MyHandlerAdapter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ControllerV4HandlerAdapter implements MyHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV4);
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {

        // 구현 Controller 객체를 부모 interface 타입으로 캐스팅
        ControllerV4 controller = (ControllerV4) handler;

        // 요청 param 값을 map 으로 변환
        Map<String, String> paramMap = createParamMap(request);
        // servlet 으로부터 독립시키기 위해 business 로직의 결과를 담을 map 생성
        HashMap<String, Object> model = new HashMap<>();

        // Controller 실행
        // 실행의 결과로 논리 이름 값을 반환한다.
        String viewName = controller.process(paramMap, model);

        // 논리 이름과 model 값을 입력한 Model View 객체 생성
        ModelView mv = new ModelView(viewName);
        mv.setModel(model);
        // 생성된 model view 객체를 반환한다.
        return mv;
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
