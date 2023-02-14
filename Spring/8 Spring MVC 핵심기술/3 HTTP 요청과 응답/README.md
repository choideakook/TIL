# HTTP 요청과 응답 데이터

## ✏️ HTTP 요청 메시지

### 📍 주로 사용되는 요청 메시지 3가지

1. ***GET - Query Parameter*** [🔗](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%201%20GET%20Query%20Parameter.md)  
    - message body 없이 URL 의 쿼리 파라미터에 data 를 포함해 전달
    - 검색, 필터, 페이징 …
    - ?name=hello&age=20
2. ***POST - HTML Form*** [🔗](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%202%20POST%20HTML%20Form.md)
    - message body 에 쿼리 파라미터 형식으로 전달
    - 회원가입, 상품 주문, HTML Form …
    - content-type: application/x-www-form-urlencoded
        - HTML Form 을 통해 전달된 정보라는 뜻
3. ***HTTP message body 에 직접 data 를 담아서 요청*** [🔗](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%204%20API%20메시지%20바디%20-%20JSON.md)
    - HTTP API 에서 주로 사용
    - JSON, XML, TEXT …
        - 주로 JSON 이 사용됨
    - POST, PUT, PATCH

<br>

## ✏️ HTTP 응답 메시지

### 📍 주로 사용되는 응답 메시지 3가지

1. ***단순 Text 응답*** [🔗](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%205%20HttpServletResponse%20기본%20사용법.md)
    - *`writer*.println("Ok");`
[🔗 HTML , API 방식](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%206%20HTTP%20응답%20Data%20-%20HTML.md)
2. ***HTML 응답***
3. ***HTTP API***
    - Message body JSON 응답
