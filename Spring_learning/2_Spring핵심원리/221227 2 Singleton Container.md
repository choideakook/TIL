# Singleton Container

## ✏️ Singleton 패턴의 문제점

- 싱글톤 패턴을 구현하는 코드가 많이 들어감
- 의존관계상 클라이언트가 구체 클래스에 속하게됨 (DIP 위반)
- 클라이언트가 구체 클래스에 의존하게됨 (OCP 위반 가능성이 높아짐)
- 테스트 하기가 어려움
- 내부 속성을 변경하거나 초기화 가기 어려움
- private 생성자로 자식 클래스를 만들기 어려움
- 결론적으로 유연성이 떨어지며 안티패턴으로 불리기도 한다.

## ✏️ Singleton Container ( = Spring Container )

Singleton Container 는 싱글톤 패턴의 문제점을 해결하면서,

객체 인스턴스를 싱글톤으로 관리한다.

- 싱글톤 패턴 없이도 객체 인스턴스를 싱글톤으로 관리할 수 있음
- DIP , OCP 를 위반하지 않는다.
- test 제약과 private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있다.
- 이러한 싱글톤 객체를 생성하고 관라하는 기능을 싱글톤 “레지스트리” 라고 한다.

![IMG_0023.PNG](Singleton%20Container%20b1ce998ced304661aefa520b52088a5e/IMG_0023.png)

## ✏️ Singleton Container 로 생성한 객체의 참조값 확인하기

순수한 java 코드로 생성했던 AppConfig 는 new 로 객체를 생성해서 참조값이 달랐다.

[순수한 java 코드 바로가기]()

```java
@Test
@DisplayName("스프링 컨테이너와 싱글톤")
void springContainer (){

     // Spring Container 생성
     ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

     MemberService memberService1 = ac.getBean("memberService", MemberService.class);

     MemberService memberService2 =  ac.getBean("memberService", MemberService.class);

     System.out.println("memberService1 = " + memberService1);
     System.out.println("memberService2 = " + memberService2);

     assertThat(memberService1).isSameAs(memberService2);
}
```

🔍 출력값

참조값이 같은걸 확이할 수 있다.

```
memberService1 = hello.core.member.MemberServiceImpl@2a3c96e3
memberService2 = hello.core.member.MemberServiceImpl@2a3c96e3
```

## ✏️ Singleton 방식의 주의점

싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지하게 설계 (stateful) 하면 안된다.

❗️Spring Bean 의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.

- 무상태 (stateless) 설계
    - 특정 클라이언트에 의존적인 필드가 있으면 안된다.
    - 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
    - 가급적 읽기만 가능해야 한다.
    - 필드 대신에 자바에서 공유되지 않는 방법으로 사용해야 한다.
        - 지역변수
        - Parameter
        - ThreadLcal 등등..

### 📍 상태 유지 설계 예시

- 주문자와 주문 금액을 조회할 수 있는 예시 class

```java
package hello.core.singleton;

public class StatefulService {

    private int price;  // 상태를 유지하는 필드

    public void order(String name, int price) {
        System.out.println("name = " + name + " / price = " + price);
        this.price = price; // 문제의 원인
    }

    public int getPrice(){
        return price;
    }
}
```

- 예시 상황
    1. A사용자가 10,000 원을 주문
    2. B사용자가 20,000 원을 주문
    3. A사용자가 주문금액을 조회

```java
package hello.core.singleton;

import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;

import static org.junit.jupiter.api.Assertions.*;

class StatefulServiceTest {

    @Test
    void statefulServiceSingleton (){

        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        // ThreadA : A사용자 10,000 원 주문
        statefulService1.order("UserA", 10000);
        // ThreadB : B사용자 20,000 원 주문
        statefulService2.order("UserB", 20000);

        // ThreadA : A사용자 주문 금액 조회
        int price = statefulService1.getPrice();
        System.out.println("price = " + price); 
    }

    static class TestConfig {

        @Bean
        public StatefulService statefulService (){
            return new StatefulService();
        }
    }
}
```

🔍 출력값

```java
name = UserA / price = 10000
name = UserB / price = 20000
price = 20000
```

❗️ A사용자가 주문한 10,000 원이 출력되어야하는데 20,000 원이 조회가된다.

### 💡 문제가 발생한 원인

사용자 A 와 B 가 사용한 StatefulService 클래스는 같은 객체이기 때문에

A 가 주문한 10,000 원이 B 가 20,000 원을 주문하는 순간

문제의 원인 으로 인해 price 가 20,000 원으로 수정되어버림 

```java
package hello.core.singleton;

public class StatefulService {

    private int price;  // 상태를 유지하는 필드

    public void order(String name, int price) {
        System.out.println("name = " + name + " / price = " + price);
        this.price = price; // 문제의 원인
    }

    public int getPrice(){
        return price;
    }
}
```

결과적으로 price 는 공유되는 필드인데, 특정 클라이언트가 값을 변경한것이 문제의 원인이다.

### 📍 해결 : 무상태 설계

StatefulService 의 order 를 void 가 아닌 int 로 수정후 return 값으로 price 를 넣어 그 값을 지역 변수화 한다.

```java
package hello.core.singleton;

public class StatefulService {

    /**
     * 상태를 유지하는 설계
     */
//    private int price;  // 상태를 유지하는 필드
//
//    public void order(String name, int price) {
//        System.out.println("name = " + name + " / price = " + price);
//        this.price = price;
//    }
//    public int getPrice(){
//        return price;
//    }

    /**
     * 무상태 설계
     */
    public int order (String name , int price){
        System.out.println("name = " + name + " / price = " + price);
        return price;
    }
}
```

### 클라이언트의 주문을 지역변수화 함

```java
@Test
    void statefulServiceSingleton (){

        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        // ThreadA : A사용자 10,000 원 주문
        int userA = statefulService1.order("UserA", 10000);
        // ThreadB : B사용자 20,000 원 주문
        int userB = statefulService2.order("UserB", 20000);

        // ThreadA : A사용자 주문 금액 조회
        int price = userA;
        System.out.println("price = " + price);
    }
```

🔍 출력값

```java
name = UserA / price = 10000
name = UserB / price = 20000
price = 10000
```