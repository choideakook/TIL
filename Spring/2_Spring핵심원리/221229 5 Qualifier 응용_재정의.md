# Qualifier 응용_재정의

[ 🔗 Quailifier 의 기능과 사용 방법 ]()

## ✏️ 커스텀 @Qualifier 만들기

### 📍 새로운 Annotation Class 생성

```java
package hello.core.annotation;

public @interface MainDiscountPolicy {

}
```

<br>

### 📍 애노테이션 설정

- Qualifier 를 만들기 위해 Qualifier 원본으로 들어가 애노테이션을 복사 붙여넣기한다.

```java
package hello.core.annotation;

import java.lang.annotation.*;

@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface MainDiscountPolicy {

}
```

<br>

- @Qualifier 를 하나 더 추가해주고 이름을 설정해 준다

```java
package hello.core.annotation;

import org.springframework.beans.factory.annotation.Qualifier;

import java.lang.annotation.*;

@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier ("mainDiscoountPolicy")
public @interface MainDiscountPolicy {

}
```

<br>

- 이렇게 하면 Qualifier 괄호안의 의름설정없이 @mainDiscoountPolicy 로 대신해서 사용할 수 있다.

```java
@Component
@MainDiscountPolicy // @Qualifier ("MainDiscountPolicy") 와 동일한 기능
public class RateDiscountPolicy implements DiscountPolicy {
```

<br>

- 생성자 주입도 마찬가지

```java
@Component
//@RequiredArgsConstructor // lombok
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(
            MemberRepository memberRepository,
						// 주입을 원하는 곳에 재정의한 Qualifier 입력
            @mainDiscoountPolicy DiscountPolicy discountPolicy
    ) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```