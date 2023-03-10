# HTTP Method

## ✏️ 회원 관련 HTTP Method 를 만든다면

### 📍 API URL 설계 예시

- 회원 목록 조회
- 특정 회원 조회
- 회원 등록
- 회원 수정
- 회원 삭제

⚠️ 리소스의 식별을 가장 중요하게 생각해야 한다.

- 회원관련 DB 의 API 를 만든다고 했을 때 회원이라는 개념 자체가 리소스이다.
- 회원 등록, 수정, 조회가 리소스가 아니다.

### 📍 리소스 식별, URI 계층 구조 활용

- 회원 목록 조회 /members
- 특정 회원 조회 /members/{id}
- 회원 등록 /members/{id}
- 회원 수정 /members/{id}
- 회원 삭제 /members/{id}

⚠️ 여기서 끝나면 구별이 안됨, 리소스와 행위를 분리해 주어야함

- 리소스 : 회원
- 행위 : 등록, 조회, 삭제, 수정
- 행위가 바로 HTTP Method 이다.
    - 명사 : 리소스 (URI) , 동사 : HTTP method

❗️ 실무에서는 언제나 이렇게 맞아 떨어지지 않기 때문에 리소스에 동사를 사용하기도하고, 컨트롤러 또는 컨트롤 URI 라고 한다.

🔗 Controller , Control URI 확인하기

## ✏️ HTTP Method

[🔗 HTTP method 의 구체적인 기능](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/post%20man%20정리.md)

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

## ✏️HTTP method 의 속성

<img width="572" alt="s524" src="https://user-images.githubusercontent.com/115536240/214733748-a05905b7-64d2-42e2-aee1-e3c21a849fef.png">

### 📍 안전 - Safe Method

호출해도 리소스를 변경하지 않는 경우 안전하다고 한다.

- 호출시 리소스 (data) 가 변경될 수 있는 경우 안전하지 않다고 한다.
- 만약 무한정 같은 요청을 호출해 로그가 쌓여 장애가 발생하는 경우까지 고려하지는 않는다.

### 📍 멱등 - Idempotent Method

같은 요청을 반복해도 같은 결과물이 나오는 것을 보장할 경우 멱등하다고 한다.

- 외부요인으로 중간에 리소스가 변경되는 것 까지 고려하지 않는다.
- 멱등의 활용
    - 자동 복구 메커니즘
    - 서버가 Time Out 등올 정상 응답을 못주었을 때, 클라이언트가 같은 요청을 다시 해도 되는지를 판단하는 근거

### 📍 캐시 가능 - Cacheable Method

응답 결과 리소스를 캐시해서 사용해도 되는가?

- 실무에서는 GET, HEAD 정도만 캐시로 사용한다.
    - POST , PATCH 도 가능하긴 하지만 body 까지 캐시 키로 고려해야되서 쉽지않음
