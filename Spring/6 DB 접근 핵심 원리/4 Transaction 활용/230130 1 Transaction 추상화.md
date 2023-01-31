# Transaction 추상화

Connection 을 Parameter 로 넘기는 Transaction 방식은 JDBC 기술에 의존하고 있다.

향후 JPA 같은 다른 data 접근 기술로 변경할 경우,
Service 계층의 Transaction 관련 코드도 모두 수정되어야 하는 문제점이 있다.

⚠️ 반대로 JPA 에서 다른 data 접근 기술로 변경되어도 똑같은 문제가 발생된다.

## ✏️ Transaction 추상화로 문제 해결

Service 계층은 특정 기술에 의존하는 것이 아닌 Transaction 인터페이스에 의존하고,
특정 기술은 Transaction 인터페이스를 구체화 할 수 있게 설계한다.

- 원하는 구현체를 DI 하면 Service 계층의 수정없이 기술을 변경할 수 있게된다.
- OCP 의 원칙을 준수하게됨
- [🔗 OCP 의 원칙](https://github.com/choideakook/TIL/tree/main/Spring/2%20Spring%20핵심원리/1%20SOLID)

<br>

## ✏️ Spring 의 Transaction 추상화

Spring 에서는 이미 Transaction 을 추상화하는 인터페이스인 `PlatformTransactionManager` 를 제공하고 있다.

![s6411.png](Transaction%20%E1%84%8E%E1%85%AE%E1%84%89%E1%85%A1%E1%86%BC%E1%84%92%E1%85%AA%2023a3bc12ccbe4dd4b519a5a96c4c3092/s6411.png)

`PlatformTransactionManager` 는 이미 다양한 기술의 Transaction 구현체를 제공하기 때문에 Service 계층은 `PlatformTransactionManager` 만 의존시키면 해결된다.

### 📍 Transaction Manager

Transaction Manager 는 Transaction 의 핵심 로직을 기반으로 작동된다.

- Transaction 시작
- Commit
- Rollback