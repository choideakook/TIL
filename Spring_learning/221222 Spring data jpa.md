# Spring data jpa

관계형 데이터베이스를 사용한다면 스프링 데이터 JPA 는 필수적이다.

- 리포지토리에 구현 클래스 없이 인터페이스 만으로 개발가능.
    - 인터페이스를 통한 기본적인 CRUD
    - Method 의 이름을 통한 DB 정보 조회기능
    - 자동 페이징 기능
- 반복 개발해온 기본 CRUD 기능 제공

## ✏️ 1. 환경설정

jpa 환경설정과 동일하다.

[jpa 환경설정 바로가기](https://github.com/choideakook/Spring_Boot/blob/main/Spring_learning/221215%20JPA%20연결%20방법.md)

## ✏️ 2. Config

- member repository 의 config 코드는 필요없다
    - 3번에서 생성 할 repository 가 그 역할을 대신함

```java
		/**
     * Spring data JPA 연결시 사용
     */
    private final MemberRepository memberRepository;
    // 생성자가 한가지 밖에 없을경우 Auto wired 생략 가능
    public SpringConfig(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Bean
    public MemberService memberService(){
				// 방금 생성한 repository 로 argument 변경
        return new MemberService(memberRepository);
    }
```

## ✏️ 3. repository 구현

- interface 에서 상속은 implements 가 아닌 extends 로 한다.

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface SpringDataJpaMemberRepository
        // jpa repository 를 상속한 후 두번째 제네릭에 pk 의 데이터타입을 넣고
        // 두번째 parameter 에 Member repository 를 넣는다.
        extends JpaRepository<Member,Long>,MemberRepository {

    @Override
    Optional<Member> findByName(String name);
}
```

## ✏️ 작동 원리

1. spring data jpa 가 interface repository 를 인식해 자동으로 객체를 생성해 Spring Bean 에 업로드함
2. 개발자가 올려놓은 객체를 injection해서 사용함

### ❗️ 어떻게 인터페이스 구현 없이 프로그램이 작동되는가?

JpaRepository 에 기본적인 crud 와 단순 조회 기능은 전부 구현되어있고

그 class 를 상속한 interface repository 는 그 기능을 전부 구현 없이 사용가능하게 된다.

### 🔍 Method 확인하기

- 각각 interface 들에 속해있는 구현체를 확인하며 필요한 기능이 있을때 사용하자.

`JpaRepository` → `PagingAndSortingRepository` → `CrudRepository`

## ⚠️ Repository 에 find by name 만 코딩한 이유

기본적인 crud 와 단순 조회기능은 전부 JpaRepository 에 구현되어있지만

진행하는 프로젝트마다 data 의 정보의 내용과 명칭은 제각각 다르므로 한가지로 획일할 수 없다.

따라서 findBy ~ (~)으로 지금 프로젝트에서 사용하는 데이터의 명칭을 공유하면

자동으로 jpa 가

```java
select m from Member m where m.~ = ? 
```

이렇게 변환해준다.

만약 두가지 정보를 조회하고 싶은경우

findBy ~ And ~ (~ , ~)

이런식으로도 구현체의 이름만으로 개발이 가능하다.