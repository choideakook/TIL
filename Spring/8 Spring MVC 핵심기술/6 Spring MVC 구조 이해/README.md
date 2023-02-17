# Spring MVC - 구조 이해

## ✏️ Spring MVC 의 구조와 동작 순서

### 📍 구조 비교

- Spring MVC 구조

<img width="534" alt="s8611" src="https://user-images.githubusercontent.com/115536240/219523670-4acb2e73-c905-419c-a08d-3319db96d8c9.png">

- 직접 만든 MVC 프레임워크 구조

<img width="534" alt="s8612" src="https://user-images.githubusercontent.com/115536240/219523682-81ac3437-9669-4667-bff0-ede7f1169338.png">


- FrontController → DispatcherServlet
- handlerMappingMap → HandlerMapping
- MyHandlerAdapter → HandlerAdapter
- ModelView → ModelAndView
- viewResolver → ViewResolver
- MyView → View

<br>

### 📍 Spring MVC 동작 순서

1. ***핸들러 조회*** : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
2. ***핸들러 어댑터 조회*** : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. ***핸들러 어댑터 실행*** : 핸들러 어댑터를 실행한다.
4. ***핸들러 실행*** : 핸들러 어댑터가 실제 핸들러를 실행한다.
5. ***ModelAndView 반환*** : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 **변환**해서 반환한다.
6. ***viewResolver 호출*** : 뷰 리졸버를 찾고 실행한다.
    - ***JSP의 경우*** : InternalResourceViewResolver 가 자동 등록되고, 사용된다.
7. ***View반환*** :뷰리졸버는뷰의논리이름을물리이름으로바꾸고,렌더링역할을담당하는뷰객체를 반환한다.
    - JSP의 경우 InternalResourceView(JstlView) 를 반환하는데, 내부에 forward() 로직이 있다.
8. ***뷰렌더링*** :뷰를통해서뷰를렌더링한다.

<br>

## ✏️ Spring MVC 의 인터페이스

- Spring MVC 의 가장 큰 강점은 `DispatcherServlet` 코드의 수정 없이 원하는 기능을 변경하거나, 확장할 수 있다는 점이다.
    - 새로운 interface 를 만들어 `DispatcherServlet` 에 등록하면 나만의 Controller 를 만들 수도 있다.

<br>

### 📍 주요 interface

- 핸들러 매핑 : org.springframework.web.servlet. <U>HandlerMapping</U>
    - 직접 만들 땐 단순히 Map 으로 했지만 Spring MVC 는 interface 로 제공한다.
- 핸들러 어댑터 : org.springframework.web.servlet.<U>HandlerAdapter</U>
- 뷰 리졸버 : org.springframework.web.servlet.<U>ViewResolver</U>
    - 직접 만들 땐 단순 method 로 했지만 Spring MVC 는 interface 로 제공한다.
    - JSP 용 리졸버, thymleaf 용 리졸버 … 다양한 구현체를 갖고있다.
- 뷰 : org.springframework.web.servlet.<U>View</U>
