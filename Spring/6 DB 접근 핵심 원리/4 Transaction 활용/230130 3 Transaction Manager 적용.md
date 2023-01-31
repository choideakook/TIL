# Transaction Manager 적용

## ✏️ Repository 수정 - V3

- `DataSourceUtils` Class 를 사용해 Connection close 수정
    - Connection 을 그대로 Close 하면 동기화가 끊기게 된다.
    - `DataSourceUtils.*releaseConnection()` 는* Transaction 동기화 Mananer 에서 사용한 Con 은 살려두고,
    Transaction 과 상관없는 Con 만 Close 시킨다.
- `DataSourceUtils` Class 를 사용해 Connection 획득
    - Transaction 동기화 Manager 에 보관된 Con 을 가져오게 된다.
    - ❗️ Transaction 동기화 Manager 가 관리하는 Con 이  없는 경우 (Transaction Manager 를 사용하지 않는 경우)
    새로운 Connection 을 알아서 생성해서 반환해준다.
    - Con 을 Param 으로 넘기지 않아도 됨

```java
package hello.jdbc.repository;

import hello.jdbc.connection.DBConnectionUtil;
import hello.jdbc.domain.Member;
import lombok.extern.slf4j.Slf4j;
import org.springframework.jdbc.datasource.DataSourceUtils;
import org.springframework.jdbc.support.JdbcUtils;

import javax.sql.DataSource;
import java.sql.*;
import java.util.NoSuchElementException;

/**
 * Transaction - Transaction Manager
 * DataSourceUtils.getConnection()
 * DataSourceUtils.releaseConnection()
 */
@Slf4j
public class MemberRepositoryV3 {

    private final DataSource dataSource;

    public MemberRepositoryV3(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public Member save(Member member) throws SQLException {...}
    public Member findById(String memberId) throws SQLException {...}
    public void update(String memberId, int money) throws SQLException {...}
    public void delete(String memberId) throws SQLException {...}

    private void close(Connection con, Statement stmt, ResultSet rs) {
        JdbcUtils.closeResultSet(rs);
        JdbcUtils.closeStatement(stmt);
        // con 을 Close 하면 동기화가 끊기게 된다.
        // JdbcUtils.closeConnection(con);
        // 주의! Transaction 동기화를 사용하기 위한 Class
        DataSourceUtils.releaseConnection(con, dataSource);
    }

    private Connection getConnection() throws SQLException {
        // 주의! Transaction 동기화를 사용하기위한 Class
        // Transaction 동기화 Manager 에서 Connection 을 가져오게 된다.
        Connection con = DataSourceUtils.getConnection(dataSource);
        log.info("get connection = {}, class = {}", con, con.getClass());
        return con;
    }
}
```

<br>

## ✏️ Service 수정 -V3_1

- Transaction 구현체를 주입받을 수 있게 Transaction Mananer 인 `PlatformTransactionManager` 를 생성한다.
- Transaction 로직 수정을 수정한다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.DefaultTransactionDefinition;

import java.sql.SQLException;

/**
 * Transaction - Transaction Manager
 */
@RequiredArgsConstructor
@Slf4j
public class MemberServiceV3_1 {

    // Data Source 의존관계를 없앤다.
    // private final DataSource dataSource;

    // Transaction Manager 를 의존시킨다.
    private final PlatformTransactionManager transactionManager;
    private final MemberRepositoryV3 memberRepository;

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

    // Transaction Logic
    public void accountTransaction(String fromId, String toId, int money) throws SQLException {

        // Transaction 시작
        // new DefaultTransactionDefinition 은 Transaction 에 관련된 옵션을 설정할 수 있다.
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());

        try {
            // Business logic
            buzLogic(fromId, toId, money);

            // Transaction 성공 종료 - commit
            transactionManager.commit(status);

        } catch (Exception e) {
            // Transaction 실패 종료 - rollback
            transactionManager.rollback(status);
            throw new IllegalStateException(e);
        }
        // finally 는 더이상 필요하지 않다.
    }
}
```

<br>

## ✏️ Test

[🔗 Transaction Mananer 로 동기화](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20접근%20핵심%20원리/4%20Transaction%20활용/230130%202%20Transaction%20동기화.md)

링크의 그림처럼 DI 를 주입해주면 정상적으로 실행된다.

1. Service 의 로직이 시작되고 Transaction 이 시작 (getTransaction) 된다.
    - Transaction Manager 는 이미 DataSource 를 주입 받은 상태이다.
    - 오토커밋모드를 수동으로 변경한다.
    - connection 이 생성되고 동기화 manager 에 보관됨
2. Service 의 Business 로직이 시작되고 Repostiory method 를 호출한다.
3. repository 의 method 가 실행되면 connection 을 가져온다다.
    - DataSourceUtil 의 getConnection 이 사용됨
    - 즉 동기화 manager 에서 가져오게 된다.
4. DB 로 SQL 이 요청되고 응답된다.
5. method 의 finally 로 close 가 된다.
    - con 은 releaseConntion 의 기능으로 Service 의 로직이 끝나기 전까지 close 되지 않는다.
    - 동기화 Manager 에서 Connection 을 가져왔기 때문
6. 모든 business 로직이 끝나면 Transaction Manager 가 Transaction 을 종료하고 Connection 이 종료된다.
    - 동기화 Manager 에 있는 Connection을 가져오고 동기화에 있는 Con 은 삭제한다.
    - 오토커밋모드를 다시 자동으로 변경한다.
    - 가져온 Connection 을 Close 한다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import org.junit.jupiter.api.*;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.transaction.PlatformTransactionManager;

import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

/**
 * Transaction - Transaction Manager
 */
class MemberServiceV3_1Test {

    public static final String Member_A = "memberA";
    public static final String Member_B = "memberB";
    public static final String Member_EX = "ex";

    private MemberRepositoryV3 memberRepository;
    private MemberServiceV3_1 memberService;

    @BeforeEach
    void beforeEach() {
        // DataSource (DB 드라이버) 생성
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        // Transaction Mananer 에 data source (DB 드라이버) 주입
        PlatformTransactionManager transactionManager = new DataSourceTransactionManager(dataSource);
        // Repository 에 DataSource 주입
        memberRepository = new MemberRepositoryV3(dataSource);
        // Service Class 에 드라이버가 주입된 Mananer 와 Repository 를 주입
        memberService = new MemberServiceV3_1(transactionManager, memberRepository);
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
