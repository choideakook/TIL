# ComponentScan 탐색 위치와 기본 스캔 대상

## ✏️ 탐색할 패키지의 시작 위치 지정 방법

애노테이션 괄호 안에 시작 위치를 지정할 수 있음

- basePackages : 탐색할 패키지의 시작 위치 지정 (directory 로 지정)
- basePackageClasses : 지정한 클래스의 패키지를 시작 위치로 지정 (class 로 지정)

### 📍 Directory 로 지정하는 방법

```java
package hello.core;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(
        basePackages = "hello.core.member"
)

public class AutoAppConfig {
}
```

💡 hello.core.member 의 경로 와 해당 경로포함 하위 디렉토리 의 package 에서 Component 를 탐색하게 된다.

### 📍 두가지 이상의 directory 를 설정하는 방법

```java
@Configuration
@ComponentScan(
        basePackages = {"hello.core.member", "hello.core.service"}
)
```

### 📍Class 이름으로 설정하는 방법

- 해당 class 가 존재하는 package 를 시작으로 하위 package 까지 Component 를 탐색한다.

```java
@Configuration
@ComponentScan(
        basePackageClasses = AutoAppConfig.class
)
```

## 🔍 @ComponentScan 괄호안에 아무 경로도 적지 않을 경우

경로를 설정하지 않을경우 해당 애노테이션이 위치하고있는 package 부터 하위 package 까지만 Component 를 탐색하게 된다.

- 업무의 효율화를 위해 경로설정을 하지않고 Config Class 를 project 경로 앞으로 위치시키면 자동으로 해당 project package 만 탐색하게 된다.

### ❗️ project 를 실행시킬 수 있는 @SpringBootApplication 에 기본적으로 ComponentScan 이 포함되어 있다.

- 수동으로 Bean 을 생성하는 경우가 아닐 경우 Config Class 를 생성 할 필요조차 없다.

## ✏️ ComponentScan 의 대상

- Component : 컴포넌트 스캔에서 사용
- Controller :  MVC 컨트롤러에서 사용
- Service : 스프링 비즈니스 로직에서 사용
- Repository : 스프링 데이터 접근 계층에서 사용
- Configuration : 스프링 설정 정보에서 사용

### 🔍 Component 애노테이션의 부가 가능

- Controller : 스프링 MVC 컨트롤러로 인식
- Service : 스프링 자체에 특별한 기능은 없고, 해당 Class 에 핵심 로직이 있다는 표시
- Repository : 스프링 데이터 접근 계층으로 인식, 데이터 계층의 예외를 스프링 예외로 변환
- Configuration : 스프링 설정정보로 인식, 스프링 빈이 싱글톤을 유지하도록 처리