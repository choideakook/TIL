# Singleton & Prototype 함께 사용시 문제점

Contianer 에 프로토타입 스코프 빈을 요청하면 새로운 객체 instance 를 생성해서 반환하지만,

싱글톤과 함께 사용시 의도대로 잘동작하지 않으므로 주의해야한다.

## ✏️ 문제의 발생 원인

### 📍 Container 에 프로토타입 빈 직접 요청

당연히 새로운 instance 가 생성되기 때문에 결과값이 같더라도 참조값은 달라진다.

```java
package hello.core.scope;

import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import static org.assertj.core.api.Assertions.assertThat;

public class SingletonWithPrototypeTest1 {

    @Test
    void prototypeFind() {

        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
        PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
        PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
        prototypeBean1.addCount();
        prototypeBean2.addCount();

        assertThat(prototypeBean1.getCount()).isEqualTo(1);
        assertThat(prototypeBean1.getCount()).isEqualTo(prototypeBean2.getCount());
        assertThat(prototypeBean1).isNotSameAs(prototypeBean2);
    }

    @Scope("prototype")
    static class PrototypeBean {

        private int count = 0;

        public void addCount (){
            count++;
        }
        public int getCount(){
            return count;
        }
        @PostConstruct
        public void init () {
            System.out.println("PrototypeBean.init" + this);
        }
        @PreDestroy
        public void distroy () {
            System.out.println("PrototypeBean.distroy");
        }
    }
}
```

### 📍 싱글톤 빈에서 프로토타입 빈 사용

싱글톤 빈(ClientBean)이 의존관계 주입을 통해서 프로토타입 빈을 주입받아 사용하는 예

![IMG_0033.PNG](Singleton%20&%20Prototype%20%E1%84%92%E1%85%A1%E1%86%B7%E1%84%81%E1%85%A6%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%89%E1%85%B5%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%8C%E1%85%A6%E1%84%8C%E1%85%A5%E1%86%B7%20c31a24511f4d4921a8ea4c3f98a49d7c/IMG_0033.png)

1. ClientBean 은 싱글톤이므로, 보통 Container 생성 시점에 DI 주입과 함께 생성된다.
    - ClientBean 은 주입 시점에 Container 에 프로토타입 빈을 요청한다.
    - Container 는 프로토타입 빈을 생성해 ClientBean 에 반환한다.
    - 이때 프로토타입 빈의 count 필드 값은 0 이다.
2. 이제 ClientBean 은 프로토타입 빈을 내부 필드에 보관한다.
    - 정확히는 참조값을 보관함.

![IMG_0034.PNG](Singleton%20&%20Prototype%20%E1%84%92%E1%85%A1%E1%86%B7%E1%84%81%E1%85%A6%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%89%E1%85%B5%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%8C%E1%85%A6%E1%84%8C%E1%85%A5%E1%86%B7%20c31a24511f4d4921a8ea4c3f98a49d7c/IMG_0034.png)

1. 클라이언트 A 는 ClientBean 을 Container 에 요청해서 받는다.
    - 싱글톤이므로 항상 같은 ClientBean 이 반환된다.
2. 클라이언트 A 는 ClientBean.logic () 을 호출한다.
3. ClientBean 은 프로토타입 빈의 addCount () 를 호출해 프로토타입 빈의 count 를 증가시킨다.
    - count 의 값이 1이된다.

![IMG_0035.PNG](Singleton%20&%20Prototype%20%E1%84%92%E1%85%A1%E1%86%B7%E1%84%81%E1%85%A6%20%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%89%E1%85%B5%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%8C%E1%85%A6%E1%84%8C%E1%85%A5%E1%86%B7%20c31a24511f4d4921a8ea4c3f98a49d7c/IMG_0035.png)

1. 클라이언트 B 도 ClientBean 을 Container 에 요청해서 받는다.
    - 싱글톤이므로 B 도 같은 ClientBean 이 반환된다.
    - 이때 ClientBean 의 내부에 가지고 있는 프로토타입 빈은 과거에 이미 주입이 끝난 빈이다.
2. 클라이언트 B 는 ClientBean.logic () 을 호출한다.
3. ClientBean 은 프로토타입 빈의 addCount () 를 호출해 프로토타입 빈의 count 를 증가시킨다.
    - count 의 값이 1 에서 하나 더 증가해 2 가 된다.

<br>

### 🔍 코드 리뷰

PrototypeBean -주입→ ClientBean -logic()→ 클라이언트A / B

- DI 방식으로 주입된 PrototypeBean 은 프로토타입 스코프의 기능을 할 수 없게된다.

```java
package hello.core.scope;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

import static org.assertj.core.api.Assertions.assertThat;

public class SingletonWithPrototypeTest1 {

    @Test
    void singletonClientUsePrototype () {
        AnnotationConfigApplicationContext ac =
                new AnnotationConfigApplicationContext(
                        ClientBean.class, PrototypeBean.class
                );

        ClientBean clientBean1 = ac.getBean(ClientBean.class);
        int count1 = clientBean1.logic();
        assertThat(count1).isEqualTo(1);

        ClientBean clientBean2 = ac.getBean(ClientBean.class);
        int count2 = clientBean2.logic();
        assertThat(count2).isEqualTo(2);

    }

    @Scope("singleton")
    static class ClientBean {
				// 생성시점에 주입
        private final PrototypeBean prototypeBean;

        @Autowired
        public ClientBean(PrototypeBean prototypeBean) {
            this.prototypeBean = prototypeBean;
        }

        public int logic () {
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }

    @Scope("prototype")
    static class PrototypeBean {

        private int count = 0;

        public void addCount() {
            count++;
        }

        public int getCount() {
            return count;
        }

        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init = " + this);
        }

        @PreDestroy
        public void distroy() {
            System.out.println("PrototypeBean.distroy");
        }
    }
}
```

👉 [문제점 해결 방법]()