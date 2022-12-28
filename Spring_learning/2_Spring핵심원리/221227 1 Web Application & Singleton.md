# Web Application & Singleton

## ✏️ Singleton 없이 순수한 Java Container 의 문제점

- 웹 어플리케이션은 일반적으로 여러 클라이언트들이 동시에 요청을 한다.

![IMG_0022.PNG](Web%20Application%20&%20Singleton%205d693f4f769b487f8a43a69d4cf556c2/IMG_0022.png)

### 📍 Spring 이 업는 순수한 DI Container (AppConfig) Test

```java
package hello.core.singleton;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class SingletonTest {

    @Test
    @DisplayName("스프링 없는 순수한 DI 컨테이너")
    void pureContainer() {
        AppConfig appConfig = new AppConfig();
        // 1. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService1 = appConfig.memberService();

        // 2. 조회 : 호출할 때 마다 객체를 생성
        MemberService memberService2 = appConfig.memberService();

        // 참조값이 정말 다른지 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);

		    assertThat(memberService1).isNotSameAs(memberService2);
    }
}
```

🔍 출력물

```
memberService1 = hello.core.member.MemberServiceImpl@17f7cd29
memberService2 = hello.core.member.MemberServiceImpl@7d8704ef
```

같은 method 를 호출해도 두 객체가 다른걸 확인할 수 있음

- 실질적으로 interface 를 구현하는 구현체 까지 새로 생성되었기 때문에 실질적으로는 4개의 객체가 생성되었음
- 이런식으로 계속해서 클라이언트가 요구할때마다 객체가 생성되면 너무 비효율적이 되어버림

## ✏️ Singleton 으로 문제 해결

- 싱글톤은 객체를 한번만 생성하고 이후부터 발생되는 클라이언트의 요청은 처음에 생성된 객체를 공유하는 방법으로 메모리를 효율화 한다.

### 📍 Singleton 패턴

- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
- private 생성자를 사용해 외부에서 임의로 new 키워드를 사용 못하게 막아야 한다.
- class 이름과 동일하게 static 으로 생성해 클래스 레벨로 올려 단 하나만 존재하게 만들어준다.

### 📍 싱글톤 인스턴스 생성

```java
package hello.core.singleton;

public class SingletonService {

    private static final SingletonService instance = new SingletonService();
    
}
```

## ✏️ Singleton 만드는 방법

### 📍 싱글톤 조회 (호출) 하는 방법

- 싱글톤을 조회하기 위해서는 해당 static method 를 통하는 방법 밖에 없다.

```java
public static SingletonService getInstance (){
     return instance;
}
```

### 📍강제로 싱글톤 생성하는걸 막는 방법

- private SingletonService () {} 를 생성하면 다른 Class 에서 new 로 생성하는걸 막을 수 있다.

```java
package hello.core.singleton;

public class SingletonService {

    private static final SingletonService instance = new SingletonService();
		
		// 오직 하나의 객체만 존재해야 하므로 외부에서 생성하는걸 막아줌
    private SingletonService (){
    }
}
```

### 📍 객체 로직 호출 만들기

```java
    public void logic (){
        System.out.println("싱글톤 객체 로직 호출");
    }
```

### 📍완성된 싱글톤 코드

- 이렇게 되면 SingletonService 에서만 싱글톤을 생성 할 수 있게 된다.
- 싱글톤 패턴을 만드는 방법은 매우 다양한 방법이 존재한다.
    - 해당 방법은 가장 단순하고 안전한 방법이다.

```java
package hello.core.singleton;

public class SingletonService {

		// static 영역에 싱글톤 객체 생성
    private static final SingletonService instance = new SingletonService();

		// 싱글톤 호출 (조회)
    public static SingletonService getInstance (){
        return instance;
    }
	
		// 타 class 에서 싱글톤 생성 금지
    private SingletonService () {}

		// 싱글톤 로직 호출
    public void logic (){
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```

## ✏️ Singleton 작동 test

- 순수한 java container 와 다르게 하나의 객체로 공유되어 사용되고 있는지 확인

```java
		@Test
    @DisplayName("싱글톤 패턴을 적용한 객체 사용")
    void singletonServiceTest(){
				// singleton 은 private 로 막혀있기 때문에
				// getInstance 로 호출해서 생성
        SingletonService singletonService1 = SingletonService.getInstance();
        SingletonService singletonService2 = SingletonService.getInstance();

        System.out.println("singletonService1 = " + singletonService1);
        System.out.println("singletonService2 = " + singletonService2);

        assertThat(singletonService1).isSameAs(singletonService2);
    }
```

🔍 출력값 확인

- 두개의 객체가 공통된 참조값을 갖고있는걸 확인할 수 있다.

```
singletonService1 = hello.core.singleton.SingletonService@305b7c14
singletonService2 = hello.core.singleton.SingletonService@305b7c14
```

## ✏️ AppConfig 의 Method 들을 Singleton으로 바꾸기

method 를 수정하지 않아도 Spring Container 에 등록해놓은 method 를 spring 이 자동으로 관리해주기 때문에 개발자가 별도의 로직을 수정할 필요는 없다.