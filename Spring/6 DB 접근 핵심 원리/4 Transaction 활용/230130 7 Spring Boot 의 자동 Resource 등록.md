# Spring Boot 의 자동 Resource 등록

## ✏️ 자동 Resource 등록

### 📍 자동 Resource 등록의 필요성

Spring Boot 가 없던 시절에는 Spring Container 에 Bean 을 직접 수동으로 등록해서 사용했다.

```java
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
```

별도의 Config Class 를 생성해 DI 를 위한 Bean 을 관리하고,
구체화 Class 가 변경하면 수동으로 Config 를 수정해 변경해주었다.

<br>

### 📍 Spring Boot 로 문제 해결

- Spring Boot 는 Data Source 자동으로 Bean 에 등록해준다.
    - 개발자가 직접 등록할 경우 Spring boot 는 자동으로 등록하지 않는다.
- application.yml
    - Connection Pool 은 기본적으로 HikariDataSource 이다.
        - yml 내에서 속성도 수정할 수 있음

```java
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jdbc
    username: sa
    password:
    driver-class-name: org.h2.Driver
```

- DataSource 뿐 아니라 Transaction Manager 도 자동으로 등록해준다.
    - Transaction Manager 를 선택할 때는 현재 등록된 라이브러를 보고 판단을 한다.
        - JDBC 기술을 사용하면 DataSourceTransactionManager
        - JPA 를 사용하면 JpaTransaction Manager …

<br>

## ✏️ Spring Boot 로 자동 Resource 등록 적용

yml 에서 설정을 마치고,
config 에서 Data Source 와 Transaction Manager 를 없애준다.

- Repository 에서 Data Source 가 필요하므로 Spring 이 자동으로 Bean 에 등록한 DataSource 를 생성자로 생성해 param 에 넣어준다.
- Test 가 정상적으로 작동된다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.*;
import org.springframework.aop.support.AopUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.context.TestConfiguration;
import org.springframework.context.annotation.Bean;

import javax.sql.DataSource;
import java.sql.SQLException;

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

        private final DataSource dataSource;

        public TestConfig(DataSource dataSource) {
            this.dataSource = dataSource;
        }

        @Bean
        MemberRepositoryV3 memberRepositoryV3() {
            return new MemberRepositoryV3(dataSource);
        }

        @Bean
        MemberServiceV3_3 memberServiceV3_3() {
            return new MemberServiceV3_3(memberRepositoryV3());
        }

    }

    @AfterEach
    void afterEach() throws SQLException {
        memberRepository.delete(Member_A);
        memberRepository.delete(Member_B);
        memberRepository.delete(Member_EX);
    }

    @Test
    void AopCheck() {
        log.info("memberService class = {}", memberService.getClass());
        log.info("memberRepository class = {}", memberRepository.getClass());
        // 프록시 Class 가 사용되었는지 검증
        assertThat(AopUtils.isAopProxy(memberService)).isTrue();
        assertThat(AopUtils.isAopProxy(memberRepository)).isFalse();
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