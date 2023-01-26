# 5xx Server Error

[🔗 HTTP 상태 코드](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%201%20HTTP%20상태%20코드.md)

## ✏️서버오류

- 서버에 문제로 오류가 발생되는 경우
- 서버에 문제가 있기 때문에 재시도 하면 성공할 수도 있음

<br>

### 📍 500 Internal Server Error

서버 문제로 오류 발생

- 서버 내부 문제로 오류가 발생됨
- 애매하면 500 에러
    - 가장 많이 사용되는 에러이다.

### 📍 503 Service Unavailabe

서비스 이용불가

- 서버가 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
- Retry - After 헤더 필드로 얼마뒤에 복구되는지 보낼 수도 있음
********
