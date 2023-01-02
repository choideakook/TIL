# 빈 스코프

스코프는 Bean 이 존재할 수 있는 범위를 뜻한다.

## ✏️ Spring 이 지원하는 스코프

- 싱글톤 (기본) 👉 [싱글톤 스코프 사용 방법]()
    - 기본 스코프, Spring containerr 의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
- 프로토타입 👉 [프로토타입 사용 방법]()
    - Spring container 는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하는 짧은 범위의 스코프
    - 소멸 콜백 기능을 지원하지 않는다.

<br>

### 📍 web 관련 스코프

- request 👉 [request 스코프 사용 방법]()
    - web 요청이 들어오고 나갈때 까지 유지되는 스코프
- session
    - web 세션이 생성되고 종료될 때 까지 유지되는 스코프
- application
    - web 의 서블릿 컨텍스와 같은 범위로 유지되는 스코프

<br>

### 📍 빈 스코프 지정 방법

- Component Scan 자동 등록

```java
@Scope ("prototype")
@Component
public class Hello {
}
```

- 수동 등록

```java
@Scope ("prototype")
@Bean
prototypeBean Hello () {
		return new Hello ();
}
```