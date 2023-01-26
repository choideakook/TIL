# post man 정리

[🔗 참고 블로그](https://velog.io/@jeongssi94/Postman-API)  
[🔗 HTTP method](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/2%20HTTP%20개념과%20메서드/230126%201%20HTTP%20Method.md)

## ✏️ 기능

- get - 조회 
- post - 등록 
- put - 수정
- patch - 부분 수정 
- delete - 삭제 

<br>

- head - 메타데이터 (서버 리소스의 헤더) 취득 (GET 과 기능이 거의 비슷함)
- options - 리소스가 지원하는 메소드 취득 (주로 CORS 에서 사용)
- connect - 프록시 동작의 (대상 리소스로 식별되는 서버에 대한) 터널 접속 변경
- TRACE - 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트 수행

<br>

## ✏️ 핵심 기능

### 📍 1. GET

- 주로 data 를 읽거나 검색 할 때 사용되는 method
    - 수정할 때는 사용하지 않음
    - data 를 변경하는 연산에 사용해선 안된다.
- 실패할경우 주로 404 (Not found) , 400 (Bad request) 에러가 발생한다.
- 같은 요청을 여러번 하더라도 변함없이 항상 같은 응답을 받을 수 있다.
    - 캐싱이 가능해 같은 data 를 한번 더 조회할 경우 저장된 값을 사용해 조회속도가 빨라진다.
- 요청시에 Body 값과 Content-Type 이 비워져있다.
    - data 조회에 성공하면 Body 값에 data 값을 저장하여 성공 응답을 보낸다.

<br>

### 📍 2. POST

- 새로운 리소스를 생성 할 때 사용된다.
    - 구체적으로는 (부모 리소스의) 하위 리소스를 생성하는데 사용
- body 를 통해 들어온 data 를 처리하는 모든 기능을 수행한다.
    - 메시지 body 를 통해 서버로 요청 data 전달
    - 서버는 요청 data 를 처리함
- creation 을 성공적으로 완료하면 201 (Created) HTTP 응답을 반환한다.
- 같은 요청을 반복할경우 같은 결과물이 나오는 것을 보장하지 않는다.
    - 두 개의 같은 POST 요청을 보내면 같은 정보를 담은 두 개의 다른 resource 를 반환할 가능성이 높다.
- data 를 생성하는 것이기 때문에 요청시 Body 값과 Content-Type 값을 작성해야 한다.
    - URL 을 통해 data 를 받지 않고, Body 값을 통해서 받는다.
    - 조회에 성공하면 Body 값에 저장한 data 값을 저장하여 성공 응답을 보낸다.
    - 만약 조회를 원하지만 Body 를 원할경우 GET 은 Body 를 쓸 수 없기때문에 조회임에도 불구하고 POST 를 사용하기도 한다.
<br>
- 단순히 data 를 생성하는 것을 넘어 프로세스 처리도 할 수 있다.
    - 주문에서 결제완료 -> 배달시작 -> 배달 완료 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우
    - 이와 같이 POST 의 결과로 새로운 리소스가 생성되지 않을 수도 있음
    - 기본적으로 리소스로만 URI 를 설계해야하지만 그럴 수 없는 경우도 있다. 이럴 때 컨트롤 URI (동사) 를 사용해서 문제를 해결할 수 있다.
    - 예 ) POST /orders/{orderId}/start-delivery

<br>

### 📍 3. PUT

- 리소스를 생성 / 업데이트하기 위해 서버로 data 를 보내는 데 사용
    - 리소스가 있으면 대체
    - 리소스가 없으면 생성
    - 엄밀히 말해서 수정이 아닌 덮어쓰기임
- 클라이언트가 리소스를 식별하고 URL 을 지정 할 수 있다는 점이 POST 와의 차이점이다.
    - id 값을 지정하고 id 값의 필드값을 변경 할 수 있음
- 동일한 PUT 요청을 여러 번 호출하면 항상 동일한 결과가 생성된다.
- data 를 수정하기 때문에 요청시 Body 값과 Content-Type 값을 작성해야한다.
    - URL 을 통해서 어떠한 data 를 수정할지 Parameter 를 받는다.
    - 수정할 data 값을 Body 값을 통해서 받는다.
- 조회에 성공시 Body 값에 저장한 data 값을 저장해 성공 응답을 보낸다.

<br>

### 📍 4. PATCH
- PUT 처럼 덮어쓰기가 아닌 부분적으로 data 를 수정할 때 사용
    - PUT 은 리소스를 대체 PATCH 는 부분 

### 📍 5. DELETE

- 지정된 리소스를 삭제한다.
- 요청시 Body 값과 Content-Type 값이 비워져있다.
    - URL 을 통해 어떤 data 를 삭제할지 Parameter 를 받는다.
    - 삭제 성공시 Body 값 없이 성공 응답만 보내게 된다.

<br>

## ✏️ Status Code
[🔗 더 많은 상태 코드](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%201%20HTTP%20상태%20코드.md)

### 📍 200 - OK

- 문제없이 요청에 대한 처리가 백엔드 서버에 이루어진 후 오는 응답코드

<br>

### 📍 201 - Created

- 무언가 잘 생성되었을 때 오는 응답코드
- 보통 POST method 의 요청에 따라 백엔드 서버에 잘 생성, 수정 될경우 나타남

<br>

### 📍 400 - Bad Request

- 요청이 잘못되었을 때의 응답코드
- 주로 Body 에 보내는 내용이 잘못되었을 때 사용됨

<br>

### 📍 401 - Unauthorized

- 로그인이나 회원가입이 필요할 때 오는 응답코드

<br>

### 📍 403 - Forbidden

- 유저가 해당 요청에 대한 권한이 없을때 오는 응답코드

<br>

### 📍 404 - Not Found

- 요청된 URL 이 존재하지 않을경우 오는 응답코드

<br>

### 📍 500 - Internal Server Error

- 서버에서 에러가 났을 때 오는 응답코드
- 100% 백엔드가 잘못했을 때 발생된다.
