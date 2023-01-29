# Transaction - 적용 로직 만들기

실제 application 에서 DB Transaction 을 사용해
계좌 이체와 같은 원자성이 중요한 Business 로직을 구현해보자.

## ✏️ Service 개발

- 잔고 변경 사이에 Transaction 이 잘 작동하는지 확인하기 위해 인위적인 error 를 발생시키기 위한 if 문을 넣었다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV1;
import lombok.RequiredArgsConstructor;

import java.sql.SQLException;

@RequiredArgsConstructor
public class MemberServiceV1 {

    private final MemberRepositoryV1 memberRepository;

    public void accountTransaction(String fromId, String toId, int money) throws SQLException {
        // 잔고 변경을 위한 member 찾기
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);

        // 잔고 변경을 위한 memeber 의 money 변수화
        int fromMemberMoney = fromMember.getMoney();
        int toMemberMoney = toMember.getMoney();

        // 잔액이 부족할 경우 error
        if (fromMemberMoney - money < 0) throw new IllegalStateException("잔액이 부족합니다.");

        // 잔고 변경
        memberRepository.update(fromId, fromMemberMoney - money);
        // 이체중 예외가 발생될 경우
        if (toId.equals("ex")) throw new IllegalStateException("이체중 예외 발생");
        memberRepository.update(toId, toMemberMoney + money);
    }
}
```

<br>

## ✏️ Test 로 검증 - Transaction 이 없는 경우

### 📍 이체가 정상적으로 작동되는지 확인

- member A - 10000
- member B - 10000
- A — 2000 —> B
- 정상적으로 잘 작동된다.
    - member A - 8000
    - member B - 12000

```java
package hello.jdbc.service;

import hello.jdbc.connection.ConnectionConst;
import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV1;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

/**
 * 기본동작, Transaction 이 없어서 문제 발생
 */
class MemberServiceV1Test {

    public static final String Member_A = "memberA";
    public static final String Member_B = "memberB";
    public static final String Member_EX = "ex";

    private MemberRepositoryV1 memberRepository;
    private MemberServiceV1 memberService;

    @BeforeEach
    void beforeEach() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        memberRepository = new MemberRepositoryV1(dataSource);
        memberService = new MemberServiceV1(memberRepository);
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
}
```

### 📍 이체도중 예외가 발생될 경우

- member A - 10000
- member EX - 10000
- A — 2000 —> EX
- 중간에 예외가 발생해 A 의 2000 원만 감소했다.
    - member A - 8000
    - member B - 10000

```java
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

        assertThat(findA.getMoney()).isEqualTo(8000);
        assertThat(findB.getMoney()).isEqualTo(10000);
    }
```

<br>

### 📍 문제점

data 수정 작업 도중 중간에 문제가 생길경우 rollback 을 해야하지만

각각의 SQL 이 개별적으로 작업이 끝나면 즉시 Commit 이 되어버리는 문제가 있었다.