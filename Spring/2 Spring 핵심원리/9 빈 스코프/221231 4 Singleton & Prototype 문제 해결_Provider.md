# Singleton & Prototype 문제 해결_Provider

## ✏️ Provider 의 기능

- Dependency Lookup (DL) 을 간단하게 도와준다.
    - 의존관계를 주입 (DI) 받는게 아니라 직접 필요한 의존관계를 찾는 것을 DL (의존관계 조회 / 탐색) 라고한다.
    - 👉 [DL 자세하게 알아보기]()
- Spring Container 가 생성될 때 DI 를 할 수 없는경우 Provider 를 사용해서 문제를 해결할 수 있다.
    - 👉 [예시 상황]()
    - 👉 [Provider 를 사용한 해결방법]()

## ✏️ ObjectProvider

- ObjectRactory 를 상속함
    - ObjectRactory 는 getObject() 매소드 밖에 없다.
    - ObjectProvider 는 옵션, 스트림 처리등 편의기능이 많다.
- DL 을 간단하게 도와주는 기능을 갖고있다.
- 별도의 라이브러리가 필요없다.
- 싱글톤 빈과 프로토타입 빈을 함께 사용해도, 사용할 때 마다 항상 새로운 프로토타입 빈을 생성한다.

<br>

### 📍 ObjectProvider

getObject 를 호출할 때 Container 에서 프로토타입 빈을 찾아서 반환해주는 기능

- class 수정

```java
		@Scope("singleton")
    static class ClientBean {
//        private final PrototypeBean prototypeBean;

//        @Autowired
//        public ClientBean(PrototypeBean prototypeBean) {
//            this.prototypeBean = prototypeBean;
//        }

        @Autowired  // Test 용 필드 주입 (DI 사용해도 상관없음)
        private ObjectProvider<PrototypeBean> prototypeBeanProbider;

        public int logic () {
            // getBean 과 같은 기능임
            PrototypeBean prototypeBean = prototypeBeanProbider.getObject();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
```

- Test
    
    싱글톤을 사용했지만 다른 참조값이 출력됬고, 따라서 count 값도 둘 다 1이 출력됨
    

```java
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
        assertThat(count2).isEqualTo(1);

    }
// PrototypeBean.init = hello.core.scope.SingletonWithPrototypeTest1$PrototypeBean@5d8bafa9
// PrototypeBean.init = hello.core.scope.SingletonWithPrototypeTest1$PrototypeBean@2dca0d64
```

<br>

### ❗️ObjectProvider 의 문제점

Spring 에 의존적이다.

<br>

## ✏️ JSR - 330 Provider

⚠️  javax.inject.Provider 라는 JAVA 표준을 사용하는 방법이다.

- JSR - 330 은 JAVA 표준이라는 뜻

<br>

- DL 을 간단하게 도와주는 기능을 갖고있다.
    - 지금 상황에 딱 필요한 정도의 DL 기능만 제공한다.
- 자바 표준이다.
    - 기능이 단순해 단위테스트나, mock 코드를  만들기 훨씬 쉬워진다.
    - Spring 이 아닌 다른 Container 에서도 사용 가능하다.
- 별도 라이브러리가 필요하다. (단점)

### 📍 1. 라이브러리 추가

![스크린샷 2023-01-01 오후 8.10.36.png](Singleton%20&%20Prototype%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%8C%E1%85%A6%20%E1%84%92%E1%85%A2%E1%84%80%E1%85%A7%E1%86%AF_Provider%20030f455398d544ba8895ebe11e7ea62f/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-01-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.10.36.png)

gradle 에 javax.inject:javax.inject:1 라이브러리를 추가 한다.

❗️ Provider ( javax.inject )

<br>

### 📍 2. Class 수정

수정후 실행해보면 정상적으로 잘 작동한다.

```java
@Scope("singleton")
    static class ClientBean {
				// ObjectProvider -> Provider
        @Autowired
        private Provider<PrototypeBean> prototypeBeanProbider;

        public int logic () {
            // getObject -> get
            PrototypeBean prototypeBean = prototypeBeanProbider.get();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
```

## 🧩 **TL;DR**

### ObjectProvider

- DL 을 위한 다양한 편의기능을 제공해주고 Spring 외 별도 의존관걔 추가가 필요없어 편리하다.
- Spring 이 아닌 다른 Container 의 사용이 불가하다.
    - 하지만 이런 상황은 거의 없다.

### JSR - 330 Provider

- Spring 이 아닌 다른 Container 에서도 사용이 가능하다.

<br>

⇒ ObjectProvider 를 사용하면된다.