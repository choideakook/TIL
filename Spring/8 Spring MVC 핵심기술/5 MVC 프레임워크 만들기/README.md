# README

# MVC 프레임워크 만들기

## ✏️ Front Controller 패턴의 특징

- Front Controller 서블릿 하나로 클라이언트의 요청을 받음
- Front Controller 에서 모든 공통처리를 먼저 완료시킴
- Front Controller 를 제외한 나머지 Controller 는 서블릿을 사용하지 않아도 된다.

<br>

![s8511.png](README%20b5b6a923003e4ae99fe43d3247c1bb63/s8511.png)

<br>

⚠️ Spring 웹 MVC 도 `DispatcherServlet` 이라는 Front Controller 패턴으로 구현되어 있다.