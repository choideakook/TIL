# HTTP 요청과 응답 데이터

## ✏️ HTTP 요청 메시지

### 📍 주로 사용되는 요청 메시지 3가지

1. ***GET - Query Parameter*** 🔗
    - message body 없이 URL 의 쿼리 파라미터에 data 를 포함해 전달
    - 검색, 필터, 페이징 …
    - ?name=hello&age=20
2. ***POST - HTML Form*** 🔗
    - message body 에 쿼리 파라미터 형식으로 전달
    - 회원가입, 상품 주문, HTML Form …
    - content-type: application/x-www-form-urlencoded
        - HTML Form 을 통해 전달된 정보라는 뜻
3. ***HTTP message body 에 직접 data 를 담아서 요청*** 🔗
    - HTTP API 에서 주로 사용
    - JSON, XML, TEXT …
        - 주로 JSON 이 사용됨
    - POST, PUT, PATCH

<br>

## ✏️ HTTP 응답 메시지

### 📍 주로 사용되는 응답 메시지 3가지

1. ***단순 Text 응답*** 🔗
    - *`writer*.println("Ok");`
2. ***HTML 응답*** 🔗
3. ***HTTP API*** 🔗
    - Message body JSON 응답
