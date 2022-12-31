# Spring Bean 의 상속관계 조회

부모 타입으로 조회할경우 자식 타입도 함께 조회된다.

![스프링빈조회상속관계.PNG](Spring%20Bean%20%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A1%E1%86%BC%E1%84%89%E1%85%A9%E1%86%A8%E1%84%80%E1%85%AA%E1%86%AB%E1%84%80%E1%85%A8%20%E1%84%8C%E1%85%A9%E1%84%92%E1%85%AC%20835624ee3dee4362a0e8669d2b39ee3b/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%2591%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25BC%25E1%2584%2587%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%258C%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AC%25E1%2584%2589%25E1%2585%25A1%25E1%2586%25BC%25E1%2584%2589%25E1%2585%25A9%25E1%2586%25A8%25E1%2584%2580%25E1%2585%25AA%25E1%2586%25AB%25E1%2584%2580%25E1%2585%25A8.png)

- Java 최고 객체인 object 타입으로 조회할경우 모든 Spring Bean 을 조회할 수 있다.

## ✏️ 자식이 둘 이상인 부모타입을 조회한 경우

❗️ 그냥 조회할 경우 NoUniqueBeanDefinitionException 에러가 발생한다.

```java
package hello.core.beanFind;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import static org.junit.jupiter.api.Assertions.assertThrows;

public class ApplicationContextExtendsFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);

    @Test
    @DisplayName("부모 타입 조회할경우 자식이 둘 이상 있으면 중복 오류 발생")
    void findBeanByParentTypeDuplication (){
				DiscountPolicy rateDiscountPolicy = ac.getBean(DiscountPolicy.class);
        assertThrows(NoUniqueBeanDefinitionException.class,
                () -> ac.getBean(DiscountPolicy.class));
    }

    @Configuration
    static class TestConfig {
        @Bean
        public DiscountPolicy rateDiscountPolicy() {
            return new RateDiscountPolicy();
        }

        @Bean
        public DiscountPolicy FixDiscountPolicy() {
            return new FixDiscountPolicy();
        }
    }
}
```

## ✏️ 해결 방법

### 1. Bean 이름을 지정해 특정 Bean 만 조회하기

```java
@Test
@DisplayName("부모 타입 조회할경우 자식이 둘 이상 있는경우 빈 이름 지정")
void findBeanByParentTypeBeanName (){
		DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
}
```

### 2. 특정한 하위 타입으로 조회 (권장 X)

```java
@Test
@DisplayName("특정 하위 타입으로 조회")
void findBeanBySubType (){
     RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
     assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
}
```

### 3. 부모 타입으로 모두 조회하기

```java
@Test
@DisplayName("부모 타입으로 모두 조회")
void findAllBeanByType (){
     Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
     for (String key : beansOfType.keySet()) {
         System.out.println("key = " + key + " / value : " + beansOfType.keySet());
     }
     assertThat(beansOfType.size()).isEqualTo(2);
}
```

🔍 출력물

```
key = rateDiscountPolicy / value : [rateDiscountPolicy, FixDiscountPolicy]
key = FixDiscountPolicy / value : [rateDiscountPolicy, FixDiscountPolicy]
```