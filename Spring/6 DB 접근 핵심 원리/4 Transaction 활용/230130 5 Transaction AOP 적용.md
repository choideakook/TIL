# Transaction AOP 적용

Spring AOP 를 통한 프록시를 도입하면 Service 계층에 Transaction 코드 없이 순수 business 로직으로 작동시키는것이 가능해진다.

## ✏️ 프록시의 도입

- 프록시 도입전
    - Service 계층에 Transaction 로직이 포함되어 있다.

```java
클라이언트 --> Service 계층(tx 포함) --> Repository 계층
```

- 프록시 도입
    - Service 호출전 프록시를 호출하고 이곳에서 Transaction 을 수행한다.
    - Transaction 수행중 business 로직은 Service 계층에서 호출한다.

```java
클라이언트 --프록시호출-> 프록시(tx logic) --서비스호출-> Service 계층 --> Repository 계층
```

결과적으로 Transaction 처리하는 객체와 Business 로직을 처리하는 Sevrvice 객체가 분리되었다.

<br>

## ✏️ Transaction AOP 적용

- Service 내의 모든 Transaction 관련 로직을 제거한다.
- @Transactional 어노테이션을 달아준다.
    - Spring 이 제공하는 AOP 에 의해서 Transaction 을 구현해줌

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import lombok.extern.slf4j.Slf4j;
import org.springframework.transaction.annotation.Transactional;

import java.sql.SQLException;

/**
 * Transaction - Transaction AOP
 */
@Slf4j
public class MemberServiceV3_3 {

    private final MemberRepositoryV3 memberRepository;

    public MemberServiceV3_3(MemberRepositoryV3 memberRepository) {
        this.memberRepository = memberRepository;
    }

    // Transaction Logic
    @Transactional
    public void accountTransaction(String fromId, String toId, int money) throws SQLException {
        buzLogic(fromId, toId, money);
    }

    // Business logic
    private void buzLogic(String fromId, String toId, int money) throws SQLException {
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);

        int fromMemberMoney = fromMember.getMoney();
        int toMemberMoney = toMember.getMoney();

        if (fromMemberMoney - money < 0) throw new IllegalStateException("잔액이 부족합니다.");

        memberRepository.update(fromId, fromMemberMoney - money);

        if (toId.equals("ex")) throw new IllegalStateException("이체중 예외 발생");

        memberRepository.update(toId, toMemberMoney + money);
    }
}
```

<br>

## ✏️ Test

정상 이체는 잘 작동이 되지만,
이체중 에러 발생은 작동 오류가 된다.

```java
expected: 10000
 but was: 8000
```

즉 Transaction 이 제대로 작동하지 않았다는 뜻이다.

- Transaction AOP 를 사용하기 위해서는 Spring Container 에 Bean 을 등록해야 사용할 수 있다.
- 하지만 Test 코드를 보면 BeforeEach 를 통해 Bean 등록 없이 수동으로 DI 해서 Spring 이 인식할 수 없게 되어있다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import org.junit.jupiter.api.*;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.Transactional;

import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

/**
 * Transaction - Transaction AOP
 */
class MemberServiceV3_1Test {

    public static final String Member_A = "memberA";
    public static final String Member_B = "memberB";
    public static final String Member_EX = "ex";

    private MemberRepositoryV3 memberRepository;
    private MemberServiceV3_3 memberService;

    @BeforeEach
    void beforeEach() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        PlatformTransactionManager transactionManager = new DataSourceTransactionManager(dataSource);
        memberRepository = new MemberRepositoryV3(dataSource);
        memberService = new MemberServiceV3_3(memberRepository);
    }

    @AfterEach
    void afterEach() throws SQLException {
        memberRepository.delete(Member_A);
        memberRepository.delete(Member_B);
        memberRepository.delete(Member_EX);
    }

    @Test
    @DisplayName("정상 이체")
    void accountTransaction() throws SQLException {
        Member memberA = new Member(Member_A, 10000);
        Member memberB = new Member(Member_B, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        memberService.accountTransaction(memberA.getMemberId(), memberB.getMemberId(), 2000);

        Member findA = memberRepository.findById(memberA.getMemberId());
        Member findB = memberRepository.findById(memberB.getMemberId());

        assertThat(findA.getMoney()).isEqualTo(8000);
        assertThat(findB.getMoney()).isEqualTo(12000);
    }
    @Test
    @DisplayName("이체중 예외 발생")
    void accountTransferEx() throws SQLException {
        Member memberA = new Member(Member_A, 10000);
        Member memberB = new Member(Member_EX, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        assertThrows(IllegalStateException.class,
            ()->memberService.accountTransaction(
                    memberA.getMemberId(), memberB.getMemberId(), 2000));

        Member findA = memberRepository.findById(memberA.getMemberId());
        Member findB = memberRepository.findById(memberB.getMemberId());

        assertThat(findA.getMoney()).isEqualTo(10000);
        assertThat(findB.getMoney()).isEqualTo(10000);
    }
}
```

<br>

## ✏️ Spring Container 에 Bean 등록으로 문제 해결

Test 용 Confing class 를 만들어 Bean 을 등록해 Spring 이 인식할 수 있게 만들어주면 Transaction 이 정상적으로 작동한다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.*;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.TestConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.Transactional;

import javax.sql.DataSource;
import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

/**
 * Transaction - Transaction AOP
 */
@Slf4j
@SpringBootTest
class MemberServiceV3_1Test {

    public static final String Member_A = "memberA";
    public static final String Member_B = "memberB";
    public static final String Member_EX = "ex";

    @Autowired private MemberRepositoryV3 memberRepository;
    @Autowired private MemberServiceV3_3 memberService;

    // 임시 Config Class 를 만들어 Container 에 Bean 을 등록해준다.
    @TestConfiguration
    static class TestConfig {
        @Bean
        DataSource dataSource() {
            return new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        }

        @Bean
        PlatformTransactionManager transactionManager() {
            return new DataSourceTransactionManager(dataSource());
        }

        @Bean
        MemberRepositoryV3 memberRepositoryV3() {
            return new MemberRepositoryV3(dataSource());
        }

        @Bean
        MemberServiceV3_3 memberServiceV3_3() {
            return new MemberServiceV3_3(memberRepositoryV3());
        }

    }

    // AOP 가 정상적으로 작동되었는지 확인하기위해 log 를 남김
    @Test
    void AopCheck() {
        log.info("memberService class = {}", memberService.getClass());
        log.info("memberRepository class = {}", memberRepository.getClass());
        // 프록시 Class 가 사용되었는지 검증
        assertThat(AopUtils.isAopProxy(memberService)).isTrue();
        assertThat(AopUtils.isAopProxy(memberRepository)).isFalse();
    }

    @AfterEach
    void afterEach() throws SQLException {
        memberRepository.delete(Member_A);
        memberRepository.delete(Member_B);
        memberRepository.delete(Member_EX);
    }

    @Test
    @DisplayName("정상 이체")
    void accountTransaction() throws SQLException {
        Member memberA = new Member(Member_A, 10000);
        Member memberB = new Member(Member_B, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        memberService.accountTransaction(memberA.getMemberId(), memberB.getMemberId(), 2000);

        Member findA = memberRepository.findById(memberA.getMemberId());
        Member findB = memberRepository.findById(memberB.getMemberId());

        assertThat(findA.getMoney()).isEqualTo(8000);
        assertThat(findB.getMoney()).isEqualTo(12000);
    }
    @Test
    @DisplayName("이체중 예외 발생")
    void accountTransferEx() throws SQLException {
        Member memberA = new Member(Member_A, 10000);
        Member memberB = new Member(Member_EX, 10000);
        memberRepository.save(memberA);
        memberRepository.save(memberB);

        assertThrows(IllegalStateException.class,
            ()->memberService.accountTransaction(
                    memberA.getMemberId(), memberB.getMemberId(), 2000));

        Member findA = memberRepository.findById(memberA.getMemberId());
        Member findB = memberRepository.findById(memberB.getMemberId());

        assertThat(findA.getMoney()).isEqualTo(10000);
        assertThat(findB.getMoney()).isEqualTo(10000);
    }
}
```

### 🔍 출력물 확인

- AopCheck 의 로그를 확인해보면 Service 와 Repository 의 Class 를 확인할 수 있다.
    - Repository 는 예상대로 V3 가 get 되었지만,
    Service 는 뒤에 $$EnhancerBySpringCGLIB$$72a8fa2e 까지 get 되었다.
    - $$EnhancerBySpringCGLIB$$72a8fa2e 가 프록시 Class 라는 뜻이고,
    Service Class 를 override 한 프록시가 실행되는 원리이다.
    - 사실 Service Class 는 실행되지 않음
- Assert That 의 결과를 보아도 프록시 Class 의 사용여부를 확인할 수 있다.

```java
memberService class = class hello.jdbc.service.MemberServiceV3_3$$EnhancerBySpringCGLIB$$72a8fa2e
memberRepository class = class hello.jdbc.repository.MemberRepositoryV3
```