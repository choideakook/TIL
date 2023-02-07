# ParameterResolutionException

Parameter / Resolution / Exception

파라미터 / 해결 / 예외

직역은 뭔가 파라미터가 제대로 해결되지 않아서 발생한 예외인것 같다.

## ✏️ 발단

Spring Boot 로 JPA 를 이용해 프로젝트를 만들던 중
Test 를 실행하니 에러가 발생했다.

```java
ParameterResolutionException: 
    No ParameterResolver registered for parameter
```

<br>

## ✏️ 문제 해결

확인해보니 의존관계 주입을 `@RequiredArgsConstructor` 로 주입 받았던 것이 문제였다.

`@Autowired` 로 주입방식을 바꾸니 정상적으로 Test 가 작동되었다.

<br>

## ✏️ 원인 파악

JUnit 5 부터는 JUnit 이 스스로 DI 를 지원한다고 하고, DI 를 지원하는 타입이 정해져 있다고 한다.

Lombok 이 작동이 안되는 이유는
JUnit 이 생성자에 다른 의존성을 주입하려고 먼저 개입을 하기 때문이라고 한다.

즉 단위 테스트에서 의존성 주입을 받으려면 `@Autowired` 로 주입해주면 된다.