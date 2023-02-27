# 핵심 요약 
## ✏️ ***요청 Parameter*** VS ***HTTP message body***

### 📍 ***요청 Parameter 방식***

- `@Controller`
- 요청 Parameter 를 조회하는 기능
- return 값으로 Model and View 를 반환한다.
- 요청 - `@RequestParam` / 응답 - `@ModelAtrribute`
- SSR 방식으로 코딩할 때 사용
- <br>

### 📍 ***HTTP Message Body 방식***

- `@RestController`
- HTTP Message body 를 직접 조회하는 기능
- return 값으로 단순 JASON 이나 Text 등 을 반환한다.
- 요청 - `@RequestBody` / 응답 - `@ResponseBody`
- 단순 Text 로 통신하거나, CSR 방식으로 코딩할 때 사용

<br>

# Spring MVC Project 시작하기

### 📍 Project 생성

- spring boot - 2.7.8
- package name - hello.springmvc
- packaging - Jar
    - Jar 는 자체 서버 (Tomcat) 를 기본으로 사용하고 있고 `webapp` 경로도 사용하지 않는다.
    - 내장 서버 사용에 최적화 되어있는 기능이다.
    - 학습목적이 아닌 이상 기본적으로 사용되는 옵션이다.
- Java 11
- dependencies
    - Spring Web
    - Thymeleaf
    - Lombok

[🔗 프로젝트 기본 설정](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Intellij%20프로젝트%20생성후%20기본%20세팅.md)

<br>

### 📍 웰컴 페이지 만들기

- 경로 : resources.static.index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>로그 출력
        <ul>
        <li><a href="/log-test">로그 테스트</a></li>
        </ul>
    </li>
    <!-- -->
    <li>요청 매핑
        <ul>
            <li><a href="/hello-basic">hello-basic</a></li>
            <li><a href="/mapping-get-v1">HTTP 메서드 매핑</a></li>
            <li><a href="/mapping-get-v2">HTTP 메서드 매핑 축약</a></li>
            <li><a href="/mapping/userA">경로 변수</a></li>
            <li><a href="/mapping/users/userA/orders/100">경로 변수 다중</a></li>
            <li><a href="/mapping-param?mode=debug">특정 파라미터 조건 매핑</a></li>
            <li><a href="/mapping-header">특정 헤더 조건 매핑(POST MAN 필요)</a></li>
            <li><a href="/mapping-consume">미디어 타입 조건 매핑 Content-Type(POST MAN 필요)</a></li>
            <li><a href="/mapping-produce">미디어 타입 조건 매핑 Accept(POST MAN 필요)</a></li>
        </ul>
    </li>
    <li>요청 매핑 - API 예시
        <ul>
            <li>POST MAN 필요</li>
        </ul>
    </li>
    <li>HTTP 요청 기본
        <ul>
            <li><a href="/headers">기본, 헤더 조회</a></li>
        </ul>
    </li>
    <li>HTTP 요청 파라미터
        <ul>
            <li><a href="/request-param-v1?username=hello&age=20">요청 파라미터v1</a></li>
            <li><a href="/request-param-v2?username=hello&age=20">요청 파라미터v2</a></li>
            <li><a href="/request-param-v3?username=hello&age=20">요청 파라미터v3</a></li>
            <li><a href="/request-param-v4?username=hello&age=20">요청 파라미터v4</a></li>
            <li><a href="/request-param-required?username=hello&age=20">요청 파라미터 필수</a></li>
            <li><a href="/request-param-default?username=hello&age=20">요청 파라미터 기본 값</a></li>
            <li><a href="/request-param-map?username=hello&age=20">요청 파라미터MAP</a></li>
            <li><a href="/model-attribute-v1?username=hello&age=20">요청 파라미터 @ModelAttribute v1</a></li>
            <li><a href="/model-attribute-v2?username=hello&age=20">요청 파라미터 @ModelAttribute v2</a></li>
        </ul>
    </li>
    <li>HTTP 요청 메시지
        <ul>
            <li>POST MAN</li>
        </ul>
    </li>
    <li>HTTP 응답 - 정적 리소스, 뷰 템플릿
        <ul>
            <li><a href="/basic/hello-form.html">정적 리소스</a></li>
            <li><a href="/response-view-v1">뷰 템플릿 v1</a></li>
            <li><a href="/response-view-v2">뷰 템플릿 v2</a></li>
        </ul>
    </li>
    <li>HTTP 응답 - HTTP API, 메시지 바디에 직접 입력
        <ul>
            <li><a href="/response-body-string-v1">HTTP API String v1</a></li>
            <li><a href="/response-body-string-v2">HTTP API String v2</a></li>
            <li><a href="/response-body-string-v3">HTTP API String v3</a></li>
            <li><a href="/response-body-json-v1">HTTP API Json v1</a></li>
            <li><a href="/response-body-json-v2">HTTP API Json v2</a></li>
        </ul>
    </li>
</ul>
</body>
</html>
```
