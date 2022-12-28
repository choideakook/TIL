# @Configuration 과 Singleton

## 💡 궁금증

member service 와 order service 모두 interface 를 통해 new memory member repository 를 생성하고 있다.

이 경우 member service 와 order service 를 호출하면  각각 다른 repository 가 생성될것 같은데,

Singleton 은 어떻게 작동되고 있을까?

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
//import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepositry;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MemberRepository memberRepository (){
        return new MemoryMemberRepositry();
    }

    @Bean
		public DiscountPolicy DiscountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
    @Bean
    public MemberService memberService (){
        return new MemberServiceImpl(memberRepository());
    }
    @Bean
    public OrderService orderService (){
        return new OrderServiceImpl(memberRepository(), DiscountPolicy());
    }

}
```

### 📍 궁금증 확인방법

member service impl 과 order service impl 이 의존하고있는 member repository 를 비교해보자

- 각각 class 에 getMemberRepository 를 생성

```java
		//Singleton test
    public MemberRepository getMemberRepository(){
        return memberRepository;
    }
```

- Test case 에서 두 getMemberRepository 의 참조값을 비교

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberServiceImpl;
import hello.core.order.OrderServiceImpl;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class ConfigurationSingletonTest {

    @Test
    void configurationConfigTest (){
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
        MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

        MemberRepository memberRepository1 = memberService.getMemberRepository();
        MemberRepository memberRepository2 = orderService.getMemberRepository();

        System.out.println("memberService -> repository = " + memberRepository1);
        System.out.println("orderService -> repository = " + memberRepository2);
        System.out.println("memberRepository = " + memberRepository);
    }

}
```

🔍 출력값

3가지 루트로 가져온 참조값이 전부 동일하게 출력되었다.

```java
memberService -> repository = hello.core.member.MemoryMemberRepositry@35e5d0e5
orderService -> repository = hello.core.member.MemoryMemberRepositry@35e5d0e5
memberRepository = hello.core.member.MemoryMemberRepositry@35e5d0e5
```

❗️ 만약 참조값이 다르다면 Configuration 의 method 가 static 이 붙어있거나 @Configuration 이 없는지 확인

- Bean 에 정적 method 인 static 이 선언되어있으면 Singleton 보장이 적용되지 않는다.
- @Configuration 이 없으면 Spring Container 에 등록될때 순수 자바 코드를 기반으로 등록이 되어서 싱글톤 보장이 적용되지 않는다.

### 📍 Singleton 이 보장되었다는 증거

AppConfig 에 method 가 호출될 때 마다 매소드명을 출력하도록 만들어서 어떻게 작동되는지 확인하는 방법

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
//import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepositry;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MemberRepository memberRepository (){
        System.out.println("Call AppConfig.memberRepository");
        return new MemoryMemberRepositry();
    }

    @Bean
    public DiscountPolicy DiscountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
    @Bean
    public MemberService memberService (){
        System.out.println("Call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }
    @Bean
    public OrderService orderService (){
        System.out.println("Call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), DiscountPolicy());
    }

} 
```

### 🔍 출력값 확인

```java
Call AppConfig.memberRepository
Call AppConfig.memberService
Call AppConfig.orderService
```

service 를 실행하려면 repository 를 생성해야 하므로 memberRepository 가 3번 출력되어야 하지만 실제 출력값에선 한번밖에 출력되지않았다.

결과적으로 memberRepository 는 한번 호출된 후 Singleton 을 보장받아 이후부터는 생성된 instance 로 공유되고있다는 뜻이다.