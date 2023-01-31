# Transaction AOP 정리

## ✏️ Transaction AOP 의 전체 흐름

![s64e1.png](Transaction%20AOP%20%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%B5%208a22b32448ed439eb70fbbd55a150a6d/s64e1.png)

1. 클라이언트의 프록시 호출
2. 프록시 Method 의 Transaction 이 시작하고 Transaction Manager 를 획득한다.
3. Transaction Manager 가 DB 드라이버를 이용해 getConnection 을 호출한다.
4. Data Source (DB 드라이버) 를 이용해 Connection 이 생성된다.
5. 오토 커밋 모드를 수동으로 변경한다.
6. 동기화 Manager 에 Connection 을 보관한다.
7. 
8. 프록시 Method 가 Service 의 Business 로직을 호출해 실행한다.
9. Repository 에서 getConnection 으로 동기화 Manager 에 있는 Connection 을 가져와 SQL 을 요청 응답한다.

<br>

## ✏️ 선언적 Transaction 관리

@Transactional 어노테이션을 선언해 매우 편리하게 Transaction 을 적용하는 방식

<br>

## ✏️ 프로그래밍 방식 Transaction 관리

직접 Transaction 관련 코드를 개발해 사용하는 방식이다.