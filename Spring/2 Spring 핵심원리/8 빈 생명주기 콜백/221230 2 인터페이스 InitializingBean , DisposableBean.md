# 인터페이스 InitializingBean , DisposableBean

## ✏️ 인터페이스 빈 생명주기 콜백 사용방법

### 📍초기화 콜백 : InitializingBean

NetworkClient Class 에 InitializingBean 를 implements 해준다.

```java
public class NetworkClient implements InitializingBean {
```

의존관계 주입 이후 초기화 콜백을 보내기위한 Method 인 afterPropertiesSet 를생성한다.

- InitializingBean 에서 O + E 로 method 생성

```java
// 초기화 Bean : 의존관계가 끝나면 호출
    @Override
    public void afterPropertiesSet() throws Exception {

    }
```

객체가 생성될때 connect 했던 로직을 afterPropertiesSet() 로 옮겨준다.

- 이렇게 하면 NetworkClient 가 생성될 땐 객체 생성만 된다.
- 의존관계 주입이 끝나고 콜백 method 가 호출되면 connect 가 된다.

```java
package hello.core.lifecycle;

import org.springframework.beans.factory.InitializingBean;

public class NetworkClient implements InitializingBean {

    // 접속해야 할 서버의 url
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
//        connect();
//        call("초기화 연결 메시지");
    }

		...

    // 초기화 Bean : 의존관계가 끝나면 호출
    @Override
    public void afterPropertiesSet() throws Exception {
				connect();
        call("초기화 연결 메시지");
    }
}
```

<br>

### 📍 소멸전 콜백 : DisposableBean

NetworkClient Class 에 InitializingBean 를 implements 해준후 destroy() method 생성

```java
package hello.core.lifecycle;

import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

public class NetworkClient implements InitializingBean, DisposableBean {

    // 접속해야 할 서버의 url
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
    }

		...

    // 초기화 Bean : 의존관계가 끝나면 호출
    @Override
    public void afterPropertiesSet() throws Exception {
        connect();
        call("초기화 연결 메시지");
    }

		// Bean 소멸전 호출
    @Override
    public void destroy() throws Exception {

    }
}
```

소멸전 호출될 destroy() 내부에서 disconnect() Method 를 호출해준다.

```java
		@Override
    public void destroy() throws Exception {
        disconnect();
    }
```

<br>

### 🔍 결과물 출력

생성자 호출단계의 url 에서는 여전히 null 로 나오지만,

이후부터는 url 값이 정상적으로 주입된 상태로 로직이 작동되었다.

```java
생성자 호출, url = null
connect http://www.naver.com
Call : http://www.naver.com , message = 초기화 연결 메시지
close : http://www.naver.com
```

<br>

## ✏️ 초기화, 소멸 interface 의 단점

- 이 인터페이스는 Spring 전용 interface 때문에 해당 코드가 Spring 전용 interface 에 의존한다.
- 초기화, 소멸 method 의 이름을 변경할 수 없다.
- 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.
- 이 방법은 Spring 초창기에 나온 방법들이고, 지금은 더 좋은 방법이 있어서 거의 사용하지 않는 방법이다.