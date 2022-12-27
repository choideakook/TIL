# 동일한 타입의 Spring Bean 조회방법

일반적으로 Spring Container 에 등록된 Bean 이 동일한 type 이 두개 이상일 때 type 으로 조회할 경우 오류가 발생하는데

이 경우에는 Bean 이름을 지정해서 해결할 수 있다.

🔍 ac.getBeansOfType () → 해당 타입의 모든 Bean 을 조회하는 method

## ✏️ 예제 코드

NoUniqueBeanDefinitionException 에러 발생

```java
package hello.core.beanFind;

import hello.core.AppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepositry;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

public class ApplicationContextSameBeanFindTest {

		// 예시를 위해 해당 class 에서만 사용가능한 static class 생성
    @Configuration
    static class SameBeanConfig {
	      // 동일한 타입의 두가지 class 생성
        @Bean
        public MemberRepository memberRepository1 () {
            return new MemoryMemberRepositry();
        }
        @Bean
        public MemberRepository memberRepository2 () {
            return new MemoryMemberRepositry();
        }
    }

		// Spring Container생성
    ApplicationContext ac 
				= new AnnotationConfigApplicationContext(
							SameBeanConfig.class
				);    // 괄호안의 내용을 static class 로 변경해줌
    
    @Test
    @DisplayName("동일한 타입이 중복된 경우")
    void findBeanTypeDuplicate () {
        ac.getBean(MemberRepository.class);
    }
		// NoUniqueBeanDefinitionException 에러 발생
}
```

## ✏️ 해결방법

### 1. 이름을 지정해서 특정 Bean 만 조회

- 앞의 에러가 발생하는 코드는 assertThrows 로 에러가 발생하면 성공으로 코드수정
- getBean 의 Bean 이름과 타입을 두가지 다 적어서 문제 해결

```java
		@Test
    @DisplayName("동일한 타입이 중복된 경우")
    void findBeanTypeDuplicate () {
        assertThrows(NoUniqueBeanDefinitionException.class,
                () -> ac.getBean(MemberRepository.class));
    }
    @Test
    @DisplayName("동일한 타입일경우 이름을 지정")
    void findBeanByName () {
        Object memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
        assertThat(memberRepository).isInstanceOf(MemberRepository.class);
    } 
```

### 2. 중복이 되는 모든 Bean 을 조회

```java
		@Test
    @DisplayName("특정 타입을 모두 조회")
    void findAllBeanByType () {
        Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " / value = " + beansOfType.get(key));
        }
        System.out.println("beansOfType = " + beansOfType);
        assertThat(beansOfType.size()).isEqualTo(2);
    }
```

🔍 출력 결과물

- 모든 Bean 이 출력됨

```
key = memberRepository1 / value = hello.core.member.MemoryMemberRepositry@3c9bfddc
key = memberRepository2 / value = hello.core.member.MemoryMemberRepositry@1a9c38eb
beansOfType = {memberRepository1=hello.core.member.MemoryMemberRepositry@3c9bfddc, memberRepository2=hello.core.member.MemoryMemberRepositry@1a9c38eb}
```