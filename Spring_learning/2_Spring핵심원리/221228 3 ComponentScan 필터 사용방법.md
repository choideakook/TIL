# ComponentScan 필터 사용방법

- includeFilters : 컴포넌트 스캔 대상을 추가로 지정한다.
- excludeFilters : 컴포넌트 스캔에서 제외할 대상을 지정한다.

## ✏️ 필터 사용 방법을 알아보기 위한 예제 만들기

### 📍 1. 애노테이션 직접 만들기 (애노테이션 재정의)

[ 애노테이션 재정의 자세한 내용 ]()

애노테이션을 재정의 하려면 애노테이션 class 를 생성해야 한다.

```java
public @interface AnnotationName {
}
```

- Include Component
    - Component 관련 재정의를 하기위해서 @Component 애노테이션에 들어가서 관련 애노테이션을 복사해온다.

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE) // type = class 레벨
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}

// -> @MyIncludeComponent 생성완료
```

❗️ @Target : 해당 애노테이션이 어느 레벨에 붙이는 애노테이션인지 설정

- Exclude Component

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
// -> @MyExcludeComponent 생성완료
```

### 📍 2. 애노테이션을 적용 할 Class 생성

- BeanA - include

```java
package hello.core.scan.filter;

@MyIncludeComponent
public class BeanA {
}
```

- BeanB - exclude

```java
package hello.core.scan.filter;

@MyExcludeComponent
public class BeanB {
}
```

### 📍 3. 테스트용 Configuration class 생성

Component Scan 에 filter 적용

- @ComponentScan 애노테이션의 Filter method 사용
- @ComponentScan.Filter ( type , classes )
    - ComponentScan 은 A + E 로 import 해서 생략 가능 (→@Filter)
    - type = FilterType.ANNOTATION 은 기본값이다.
        - 애노테이션 기반으로 필터링이 들어가는 설정
        - 기본값 이기 때문에 생략가능

```java
@Configuration
@ComponentScan(
		// 포함하는 필터 (@MyIncludeComponent 에서 탐색해라)
    includeFilters = @Filter(
           type = FilterType.ANNOTATION  // type = FilterType.ANNOTATION 는 기본값이라 생략 가능하다.
                , classes = MyIncludeComponent.class
	  ),
		// 제외하는 필터 (@MyExcludeComponent 는 제외해라)
		excludeFilters = @Filter(
           type = FilterType.ANNOTATION
                , classes = MyExcludeComponent.class
    )
)
static class ComponentFilterAppConfig {

}
```

## 📍 4. 잘 작동하는지 Test

- BeanA 는 include filter 에 존재하므로 탐색 성공
- BeanB 는 exclude filter 에 존재하므로 탐색 실패

```java
package hello.core.scan.filter;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.springframework.context.annotation.ComponentScan.*;

public class ComponentFilterAppConfigTest {

    @Test
    void filterScan () {
        ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);

        BeanA beanA = ac.getBean("beanA", BeanA.class);
        assertThat(beanA).isNotNull();

        assertThrows(NoSuchBeanDefinitionException.class,
                () -> ac.getBean("beanB", BeanB.class));
    }

    @Configuration
    @ComponentScan(
            includeFilters = @Filter( classes = MyIncludeComponent.class ),
            excludeFilters = @Filter( classes = MyExcludeComponent.class )
    )
    static class ComponentFilterAppConfig {
    }
}
```

## ✏️ Filter Type 의 옵션

Filter Type 은 5가지 옵션이 있다.

- ANNOTATION : 기본값으로 생각 가능하고, 애노테이션을 인식해 동작
- ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식해서 동작
- ASPECTJ : Aspect J 패턴 사용
- REGEX : 정규 표현식
- CUSTOM : TypeFilter 라는 인터페이스를 구현해서 처리