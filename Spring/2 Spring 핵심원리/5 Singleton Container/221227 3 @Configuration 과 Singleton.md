# @Configuration ๊ณผ Singleton

## ๐กย ๊ถ๊ธ์ฆ

member service ์ order service ๋ชจ๋ interface ๋ฅผ ํตํด new memory member repository ๋ฅผ ์์ฑํ๊ณ  ์๋ค.

์ด ๊ฒฝ์ฐ member service ์ order service ๋ฅผ ํธ์ถํ๋ฉด  ๊ฐ๊ฐ ๋ค๋ฅธ repository ๊ฐ ์์ฑ๋ ๊ฒ ๊ฐ์๋ฐ,

Singleton ์ ์ด๋ป๊ฒ ์๋๋๊ณ  ์์๊น?

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

### ๐ย ๊ถ๊ธ์ฆ ํ์ธ๋ฐฉ๋ฒ

member service impl ๊ณผ order service impl ์ด ์์กดํ๊ณ ์๋ member repository ๋ฅผ ๋น๊ตํด๋ณด์

- ๊ฐ๊ฐ class ์ getMemberRepository ๋ฅผ ์์ฑ

```java
		//Singleton test
    public MemberRepository getMemberRepository(){
        return memberRepository;
    }
```

- Test case ์์ ๋ getMemberRepository ์ ์ฐธ์กฐ๊ฐ์ ๋น๊ต

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

๐ย ์ถ๋ ฅ๊ฐ

3๊ฐ์ง ๋ฃจํธ๋ก ๊ฐ์ ธ์จ ์ฐธ์กฐ๊ฐ์ด ์ ๋ถ ๋์ผํ๊ฒ ์ถ๋ ฅ๋์๋ค.

```java
memberService -> repository = hello.core.member.MemoryMemberRepositry@35e5d0e5
orderService -> repository = hello.core.member.MemoryMemberRepositry@35e5d0e5
memberRepository = hello.core.member.MemoryMemberRepositry@35e5d0e5
```

โ๏ธ ๋ง์ฝ ์ฐธ์กฐ๊ฐ์ด ๋ค๋ฅด๋ค๋ฉด Configuration ์ method ๊ฐ static ์ด ๋ถ์ด์๊ฑฐ๋ @Configuration ์ด ์๋์ง ํ์ธ

- Bean ์ ์ ์  method ์ธ static ์ด ์ ์ธ๋์ด์์ผ๋ฉด Singleton ๋ณด์ฅ์ด ์ ์ฉ๋์ง ์๋๋ค.
- @Configuration ์ด ์์ผ๋ฉด Spring Container ์ ๋ฑ๋ก๋ ๋ ์์ ์๋ฐ ์ฝ๋๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ๋ฑ๋ก์ด ๋์ด์ ์ฑ๊ธํค ๋ณด์ฅ์ด ์ ์ฉ๋์ง ์๋๋ค.

### ๐ย Singleton ์ด ๋ณด์ฅ๋์๋ค๋ ์ฆ๊ฑฐ

AppConfig ์ method ๊ฐ ํธ์ถ๋  ๋ ๋ง๋ค ๋งค์๋๋ช์ ์ถ๋ ฅํ๋๋ก ๋ง๋ค์ด์ ์ด๋ป๊ฒ ์๋๋๋์ง ํ์ธํ๋ ๋ฐฉ๋ฒ

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

### ๐ย ์ถ๋ ฅ๊ฐ ํ์ธ

```java
Call AppConfig.memberRepository
Call AppConfig.memberService
Call AppConfig.orderService
```

service ๋ฅผ ์คํํ๋ ค๋ฉด repository ๋ฅผ ์์ฑํด์ผ ํ๋ฏ๋ก memberRepository ๊ฐ 3๋ฒ ์ถ๋ ฅ๋์ด์ผ ํ์ง๋ง ์ค์  ์ถ๋ ฅ๊ฐ์์  ํ๋ฒ๋ฐ์ ์ถ๋ ฅ๋์ง์์๋ค.

๊ฒฐ๊ณผ์ ์ผ๋ก memberRepository ๋ ํ๋ฒ ํธ์ถ๋ ํ Singleton ์ ๋ณด์ฅ๋ฐ์ ์ดํ๋ถํฐ๋ ์์ฑ๋ instance ๋ก ๊ณต์ ๋๊ณ ์๋ค๋ ๋ป์ด๋ค.