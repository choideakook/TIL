# HttpServletRequest 개요

## ✏️ HttpServletRequest 의 역할

HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신 HTTP 요청 메시지를 파싱하고,

그 결과를 `HttpServletRequest` 객체에 담아서 제공한다.

<br>

### 📍HTTP 요청 메시지

[🔗 HTTP 메시지](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/2%20HTTP%20개념과%20메서드/230120%201%20모든것이%20HTTP.md)

```
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20
```

- Start Line
    - HTTP method
        - POST
    - URL
        - /save
    - Query String
        - url 에 입력된 parameter 정보
    - Schema, protocol
        - HTTP/1.1
        - 스키마는 HTTP 를 뜻함
        - 프로토콜은 HTTP 의 버전을 뜻함
- Header
    - Host URL
    - Content - type
- Body
    - form 파라미터 형식 조회
    - message body 데이터 직접 조회

### 📍 HttpServletRequest 객체의 추가 기능

- 임시 저장소 기능
    - 해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능
        - 저장 : `request.setAttribute(name, value)`
        - 조회 : `request.getAttribute(name)`
- 세션 관리 기능
    - 예를들어 클라이언트의 로그인 여부를 확인하고 로그인을 유지시킬 수 있음
    - `request.getSession(create: true)`