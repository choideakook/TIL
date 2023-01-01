# NoUniqueBeanDefinitionException

조회할 빈이 2개 이상일 때 발생하는 에러

```java
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException:
	Error creating bean with name 'orderServiceImpl' defined in file [/Users/choedaegug/Desktop/대국이/Coding/core/out/production/classes/hello/core/order/OrderServiceImpl.class]: 
	Unsatisfied dependency expressed through constructor parameter 1; 
	nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException:
	No qualifying bean of type 'hello.core.discount.DiscountPolicy'
	available: expected single matching bean but found 2: rateDiscountPolicy,DiscountPolicy
```

Component Scan 을 하던중 interface 에 주입할 구현체가 2개 이상일 때 발생한다.

- 해당 에러 메세지에서는 rateDiscountPolicy , DiscountPolicy 이렇게 두가지 구현체가 문제가 있다고 한다.

## ✏️ 해결 방법

### 📍 1. Autowired 필드명 매칭

조회할 빈이 2개 이상일 때 빈의 이름과 생성자의 parameter 값의 이름이 일치하면 그 이름의 구현체를 interface 에 주입한다.

🔍 Autowired 매칭 과정

- 타입 매칭 : 파라미터 값과 타입이 일치하는 구현체를 주입한다.
- 필드명 매칭 : 공통된 타입이 2개 이상일경우 필의 이름과 파라미터 이름이 동일한 구현체를 주입한다.

### 📍 2. Quailifier 사용

추가 구분자 @Quailifier 를 붙이면 Component 에 이름을 붙일 수 있음

- RateDiscountPolicy 를 구현체로 사용하기 위해 main 이라는 이름을 부여함

```java
@Component
@Qualifier ("main")
public class RateDiscountPolicy implements DiscountPolicy {
```

- 생성자 Parameter 에 Quailifier 해당 를 넣어준다.
    - 구현체 마다 이름을 다르게 설정해 원하는 Quilifier 이름으로 중복을 막을 수 있음

```java
@Component
//@RequiredArgsConstructor // lombok
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(
            MemberRepository memberRepository,
            @Qualifier("main") DiscountPolicy discountPolicy
    ) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```

🔍 Quailifier 매칭 과정

- Quailifier 의 이름이 맞는 것 끼리 매칭한다.
- Quailifier 이름이 맞는게 없을경우 빈 이름과 Quailifier 이름이 동일한것을 매칭한다.
- 그래도 없으면 오류가 발생한다.

[ Quailifier 응용 _ 재정의 ]()

### 📍 Primary 사용

공통되는 구현체들중 Primary 애노테이션이 있는 구현체를 최우선으로 주입한다.

```java
@Primary
@Component
public class RateDiscountPolicy implements DiscountPolicy {
```

구현체에 애노테이션 한번만 붙여주면 모든 작업이 끝나기 때문에 코드가 간결하고 깔끔하다.

## ✏️ 정리

예를들어

코드에서 자주 사용하는 메인 DB 커넥션을 획득하는 스프링 빈과,

특별한 기능으로 코드에서 가끔 사용하는 서브 DB 커넥션을 획득하는 스프링 빈이 있을경우

메인 DB 커넥션은 @Primary 를 붙여주고,

서브 DB 커넥션 을 획득해야 할 때만 Quilifier 를 사용해서 명시적으로 획득하면 코드를 깔끔하게 유지할 수 있다.

## ✏️ 우선순위

Spring 에서 우선순위는 언제나 구체적이고 명시적인쪽에 있다.

Quailifier > Primary