# Transaction 의 문제점

## ✏️ Application 의 구조

각각의 계층은 담당하는 역할이 있고 하나의 계층의 구현 방식이 변경되더라도 다른 계층에 영향을 주어선 안된다.

```java
Controller      --> Service --> Repository   --> DB 서버
(프레젠테이션 계층)                  Data 접근 계층
```

### 프레젠테이션 계층 (Controller)

- URI 와 관련된 처리를 담당
- 웹 요청과 응답을 관리
- 사용자가 요청을 검증한다.
- 주로 사용되는 기술 : 서블릿, HTTP, MVC …

### Service 계층

- 핵심 Business 로직을 담당
- 특정 기술에 의존하지 않고 순수한 Java 코드로 작성된다.

### Data 접근 계층

- 실제 DB 와 접근하는 코드
- 주로 사용되는 기술 : JDBC, JPA, File, Redis, Mongo …

<br>

### ⚠️ 3 가지 계층에서 Service 계층이 가장 중요한 역할을 담당한다.

- 핵심 Business 로직을 갖고있음
- 다른 계층은 더 좋은 기술이 등장할 경우 새로운 기술로 교체가 되지만,
Service 계층은 순수 Java 코드로 작성되었기 때문에 교체되지 않는다.
- Service 계층은 특정 기술에 종속되지 않는다.
- 계층을 나눈 이유도 Service 계층을 순수하게 유지시키기 위한 이유가 크다.

## ✏️ JDBC 를 이용한 Transaction 의 문제점

- Transaction 은 Business 로직이 있는 Service 계층에서 시작하는것이 이상적이다.
    - [🔗 Transaction 의 위치](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20접근%20핵심%20원리/3%20Transaction/230128%207%20Transaction%20-%20Con%20Param%20방식.md)
- 문제는 connetion 을 parameter 로 전달하는 Transaction 방식을 사용하기 위해서는 JDBC 기술에 의존해야만 한다.
    - Service 로직보다 Transaction 을 위한 로직이 더 많아진다.
    - Service 계층의 순수성이 지켜지지 않게된다.
- 핵심 business 로직과 Transaction 로직이 섞여 유지보수하기 어렵고 가독성도 좋지 않다.
- 예외가 발생할때마다 throw 를 통한 예외 누수 문제도 발생하게 된다.

### 📍 문제는 3가지로 정리할 수 있다.

1. Transaction 동기화 문제
    - 같은 Connection 을 유지하기 위해 Param 으로 넘겨야한다.
        - 똑같은 기능도 Transaction 용과 Transaction 을 적용하지 않는 용으로 분리해야 한다.
2. 예외 누수 문제
    - Repository 에서 throw 한 JDBC 예외가 Service 계층까지 전파되었다.
    - SQL Exception 은 체크 예외이기 때문에 Repository 를 호출한 Servcie 계층에서 처리하거나 다시 throw 해야한다.
    - 🔗 SQL Exception
    - SQL Exception 은 JDBC 전용 기술이기 때문에 향후 다른 data 접근기술 (예를들어 JPA) 을 적용할 경우 Reposiroy 뿐아니라 Service 계층까지 코드를 수정해야하는 문제가 발생한다.
        - Repository 만 JPA 로 변경하는것이 아닌 Servcie 계층도 변경이 필요하게됨
        - Servcie 계층의 순수성이 지켜지지 않았기 때문에 발생하는 문제
3. JDBC 반복 문제
    - 거의 동일한 코드를 계속해서 반복해야 한다.