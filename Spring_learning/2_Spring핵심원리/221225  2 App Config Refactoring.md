# App Config Refactoring

### ✏️ 문제점

기존에 만든 config 는 만약 구현체가 변경될 경우 parameter 를 하나하나 모두 바꿔줘야했지만 parameter 를 method 로 만들면 가시성도 좋아지고 한번의 수정으로 모든 구현체를 바꿔줄 수 있다.

```java
package hello.core;

import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepositry;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

		// 둘다 parameter 값에 MemoryMemberRepositry 를 포함하고 있다.
		// parameter 값을 method 화 해주면 한번의 수정으로 모두 변경 가능하다.
    public MemberService memberService (){
        return new MemberServiceImpl(new MemoryMemberRepositry());
    }

    public OrderService oderService (){
        return new OrderServiceImpl(
                new MemoryMemberRepositry(), new FixDiscountPolicy()
        );
    }
}
```

## ✏️ 문제 해결 : 리팩토링

```java
package hello.core;

import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepositry;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;

public class AppConfig {

//------------ 구현 class 담당 ---------//
		// intetface 구현체 연결
    public static MemberRepositry memberRepository() {
        return new MemoryMemberRepositry();
    }

    private static DiscountPolicy DiscountPolicy() {
        return new FixDiscountPolicy();
    }

//------------ DI 담당 ---------------//
		// 호출한 생성자에 DI 주입
    public MemberService memberService (){
        return new MemberServiceImpl(memberRepository());
    }

    public OrderService oderService (){
        return new OrderServiceImpl(memberRepository(), DiscountPolicy());
    }

}
```