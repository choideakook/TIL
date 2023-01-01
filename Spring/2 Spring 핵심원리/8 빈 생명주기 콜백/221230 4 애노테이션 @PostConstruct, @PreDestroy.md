# 애노테이션 @PostConstruct, @PreDestroy

애노테이션으로 생명주기 콜백을 설정하는 방법

- 가장 많이 사용되는 방법이다.

## ✏️ 사용 방법

title 에서 느꼈겠지만 해당 애노테이션을 method 에 붙여주면 끝이다.

```java
		@PostConstruct
    public void init() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }

    @PreDestroy
    public void close() {
        System.out.println("NetworkClient.close");
        disconnect();
    }
```

<br>

### 🔍 출력물 확인

잘 작동된다.

```java
생성자 호출, url = null
NetworkClient.init
connect http://www.naver.com
Call : http://www.naver.com , message = 초기화 연결 메시지
NetworkClient.close
close : http://www.naver.com
```

<br>

## ✏️ 애노테이션 방식의 특징

- 최신 Spring 에서 가장 권장하는 방법이다.
- 애노테이션 하나만 붙이면 되므로 매우 간단하다.
- Spring 에 종속정인 기술이 아니라 JSR-250 이라는 자바 표준이다.
    - 자바 표준 기술이기 때문에 Spring 이 아닌 다른 Container 에서도 동작한다.
- Component Scan 과 잘 어울린다.
- 외부 라이브러리에 적용하지 못한다는 단점이 있다.
    - 코드를 고칠 수 없는 외부 라이브러리를 초기화, 종료 하려면 @Bean 의 기능을 사용해야한다. 👉[@Bean 기능 사용방법]()