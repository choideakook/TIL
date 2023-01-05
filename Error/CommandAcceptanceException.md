# CommandAcceptanceException

Command / Acceptance / Exception

명령 / 수락 / 예외

```java
CommandAcceptanceException: Error executing DDL
```

Entity 로 Table 생성시 DB의 약어를 table 명으로 설정해서 발생하는 문제

- 해결 EX)
    - order → orders
    - user → users
    - like → likes

나의 경우는 연결관계의 주인 설정시 name 에 띄어쓰기를 해서  문제가 발생됬다.

띄어쓰기 대신 _ 를 사용했더니 문제가 해결됬다.