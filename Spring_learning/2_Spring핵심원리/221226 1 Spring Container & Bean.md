# Spring Container & Bean

## ✏️ Spring Container 가 생성되는 과정

```java
ApplicationContext ac =
			new AnnotationConfigApplicationContext(AppConfig.class);
```

- ApplicationContext 를 Spring Container 라고 한다.
- ApplicationContext 는 interface 이다.
- AnnotationConfigApplicationContext 는 애노테이션 기반으로 만들어진 java 설정 Class 이다.
    - ApplicationContext : interface
    - AnnotationConfigApplicationContext : 구현체

## ✏️ Container 에 등록된 모든 빈 조회

Test case 를 통해 Bean 이 제대로 container 에 등록되었는지 확인하는 방법

```java
package hello.core.beanFind;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class ApplicationContextInforTest {

		// Spring Container 생성
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("모든 빈 출력하기")
    void findAllBean (){
				// Bean 의 이름을 가져오는 배열 생성
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        // 배열을 출력하기 위한 반복문 생성 (단축키 : iter)
				for (String beanDefinitionName : beanDefinitionNames) {
						// Bean 이름으로 해당 Bean 의 object 도 생성함
            Object bean = ac.getBean(beanDefinitionName);
						// bean 이름과 object 출력
            System.out.println(
											"\nName = " + beanDefinitionName
                    + "\nObject = " + bean
						);
        }
    }
}
```

### 🔍 출력 결과물

```
<----- Sprind 자체에서 확장하기위해 생성한 기반 Bean ------>

Name = org.springframework.context.annotation.internalConfigurationAnnotationProcessor
Object = org.springframework.context.annotation.ConfigurationClassPostProcessor@3569fc08

Name = org.springframework.context.annotation.internalAutowiredAnnotationProcessor
Object = org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor@20b12f8a

Name = org.springframework.context.annotation.internalCommonAnnotationProcessor
Object = org.springframework.context.annotation.CommonAnnotationBeanPostProcessor@e84a8e1

Name = org.springframework.context.event.internalEventListenerProcessor
Object = org.springframework.context.event.EventListenerMethodProcessor@2e554a3b

Name = org.springframework.context.event.internalEventListenerFactory
Object = org.springframework.context.event.DefaultEventListenerFactory@54a67a45

<------ 개발자가 직접 등록한 Spring Bean -------------->

		name 에 interface 명이 있고
		object 에 구현체 명이 있음

Name = appConfig (Class 명도 등록이됨)
Object = hello.core.AppConfig$$EnhancerBySpringCGLIB$$acbb0914@7d42c224

Name = memberRepository
Object = hello.core.member.MemoryMemberRepositry@56aaaecd

Name = DiscountPolicy
Object = hello.core.discount.RateDiscountPolicy@522a32b1

Name = memberService
Object = hello.core.member.MemberServiceImpl@35390ee3

Name = orderService
Object = hello.core.order.OrderServiceImpl@5e01a982
```

❗️ 내가 직접 등록한 Bean 뿐 아니라 Spring 자체에서 등록된 Bean 도 모두 출력이 되었다.

### 💡 내가 직접 등록한 Spring Bean 만 출력하는 방법

```java
@Test
    @DisplayName("애플리케이션 빈 출력하기")
    void findApplicationBean (){
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                Object bean = ac.getBean(beanDefinitionName);
                System.out.println(
												"\nName = " + beanDefinitionName
                        + "\nObject = " + bean
								);
            }
        }
    }
```

### 🔍 결과물 출력

```
Name = appConfig
Object = hello.core.AppConfig$$EnhancerBySpringCGLIB$$acbb0914@3569fc08

Name = memberRepository
Object = hello.core.member.MemoryMemberRepositry@20b12f8a

Name = DiscountPolicy
Object = hello.core.discount.RateDiscountPolicy@e84a8e1

Name = memberService
Object = hello.core.member.MemberServiceImpl@2e554a3b

Name = orderService
Object = hello.core.order.OrderServiceImpl@54a67a45
```

## ✏️ 기본적인 Spring Bean 조회 방법

- ac.getBean ( 빈이름 , 타입 );
- ac.getBean ( 타입 );
- 조회 대상 스프링 빈이 없을경우 에러 발생
    - NoSuchBeanDefinitionException: No bean named 'name' available

```java
package hello.core.beanFind;

import hello.core.AppConfig;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

class ApplicationContextBaiscFindTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("빈 이름으로 조회")
    void findBeanByName (){
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);

    }
    @Test
    @DisplayName("타입으로 조회")
    void findBeanByType (){
        MemberService memberService = ac.getBean(MemberService.class);
        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);

    }
    @Test
    @DisplayName("빈 이름이 없는경우")
    void findBeanByName2 (){
        MemberService memberService = ac.getBean("XXX", MemberService.class);
    }
}
```

❗️ 마지막 “빈 이름이 없는경우” 는 XXX 라는 interface 가 존재하지 않기 때문에 NoSuchBeanDefinitionException 에러가 발생한다.

- test 를 위해 에러가 발생하면 성공하는 test case 만드는 방법

```java
		@Test
    @DisplayName("빈이름이 없는경우")
    void findBeanByName2 (){
//        MemberService memberService = ac.getBean("memberService", MemberService.class);
        assertThrows(NoSuchBeanDefinitionException.class,
                () -> ac.getBean("XXX", MemberService.class));
    }
```

- 여기서 생성한 assertThrows 는 assertj 가 아닌 junit 에서 지원하는 기능임