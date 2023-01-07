# ApplicationContext

`BeanFactory` -상속→ `ApplicationContext` -상속→  -상속→ `AnnotationConfigApplicationContext`

- ApplicationContext 는 interface 이다.
- AnnotationConfigApplicationContext
    - 애노테이션 기반으로 만들어진 java 설정 Class 이다.

## ⚙️ Method

### 📍 getBean ( )

- @Configuration 으로 등록된 Bean 을 생성하는 기능
    - Class 이름은 생략 가능하다.
- 최상위 interface 인 BeanFactory 에서 재공하는 기능이다.

```java
괄호 첫번째 : Class 이름 (맨앞 알파뱃은 소문자로 적어야함)
괄호 두번째 : 생성 원하는 클래스 (타입)
ac.getBean("memberService", MemberService.class);
```

<br>

### 📍 getBeanDefinitionNames ()

- Container 에 등록된 모든 Bean 을 생성하는 기능
    - Spring 에서 확장을 위해 자체적으로 생성한 Bean 까지 모두 생성된다.

```java
ac.getBeanDefinitionNames();
```

<br>

만약 직접 생성한 Bean 만 조회하고 싶은 경우 if 문으로 필터링 할 수 있다.

```java
if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION)
```

❗️ Component Scan 으로 등록된 Bean 도 출력됨

<br>

### 📍getBeansOfType ()

- 해당 타입의 모든 Bean 을 생성하는 기능
    - 괄호 안에 생성을 원하는 클레스 타입을 입력하면 된다.

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