# Transaction - Con  Param 방식

## ✏️ Transaction 의 위치

application 에서 Transaction 는 Business logic 이 위치한 Service 계층에 있어야 한다.

- Service 계층이 실질적인 DB 수정 작업을 처리하는 로직을 구현하는 Class 이기 때문에 Transaction 도 Service 계층에서 기능해야 한다.

<br>

## ✏️ Connection 을 Parameter 로 전달하기

문제를 해결하기 위해서는 모든 작업을 하나로 묶고 종료될경우 Commit, 도중에 문제가 생기면 Rollback 을 하게 만들어야 한다.

- 이 방법으로 문제를 해결하기 위해서는 도중에 세션이 변경되어서는 안된다.
- 즉 작업이 끝날 때 까지 하나의 Connection 을 유지해야 하고 도중에 Connection 이 중단되어서는 안된다.

<br>

### 📍 RepositoryV2

- find by id 와 update 만 수정했음
- Connection 을 유지하기 위해 Param 에 Connection 을 추가함
- 기존에 있던 Connection 필드는 삭제한다.
- Connection 을 유지해야 하기 때문에 con 은 Close 하지 않는다.

```java
package hello.jdbc.repository;

import hello.jdbc.connection.DBConnectionUtil;
import hello.jdbc.domain.Member;
import lombok.extern.slf4j.Slf4j;
import org.springframework.jdbc.support.JdbcUtils;

import javax.sql.DataSource;
import java.sql.*;
import java.util.NoSuchElementException;

/**
 * JDBC - Connection 을 Parameter 로
 */
@Slf4j
public class MemberRepositoryV2 {

    private final DataSource dataSource;

    public MemberRepositoryV2(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public Member save(Member member) throws SQLException
    public void delete(String memberId) throws SQLException

    // Connection 을 유지하기 위해 Param 에 Connection 을 추가함
    public Member findById(Connection con, String memberId) throws SQLException {
        // 기존에 있던 Connection 필드는 삭제한다.
        String sql = "select * from member where member_id = ?";
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, memberId);
            rs = pstmt.executeQuery();

            if (rs.next()) {
                Member member = new Member();
                member.setMemberId(rs.getString("member_id"));
                member.setMoney(rs.getInt("money"));
                return member;
            } else {
                throw new NoSuchElementException("member not found memberId = " + memberId);
            }

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        }finally {
            // Connection 을 유지해야 하기 때문에 con 은 Close 하지 않음
            JdbcUtils.closeResultSet(rs);
            JdbcUtils.closeStatement(pstmt);
        }
    }

    public void update(Connection con, String memberId, int money) throws SQLException {
        String sql = "update member set money=? where member_id=?";
        PreparedStatement pstmt = null;

        try {
            pstmt = con.prepareStatement(sql);
            pstmt.setInt(1, money);
            pstmt.setString(2, memberId);

            int resultSize = pstmt.executeUpdate();
            log.info("resultSize={}", resultSize);

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        }finally {
            JdbcUtils.closeStatement(pstmt);
        }
    }
```

<br>

### 📍ServiceV2

- Repository V2 를 의존하게 변경했다.
- business logic 에서 con 을 argument 로 추가했다.
- buz 로직이 시작하기전 connection 을 획득하고 Transaction 을 시작한다.
- buz 로직이 성공하면 commit 예외가 발생하면 rollback 을 하게 설계했다.
- finally 에서 다음 사용자를 위해 Con close 전에 자동 commit 으로 변경했다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV2;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

/**
 * Transaction - param 연동, connection pool 을 고려한 종료
 */
@RequiredArgsConstructor
@Slf4j
public class MemberServiceV2 {

    private final DataSource dataSource;
    private final MemberRepositoryV2 memberRepository;

    // Business logic
    private void buzLogic(String fromId, String toId, int money, Connection con) throws SQLException {
        Member fromMember = memberRepository.findById(con, fromId);
        Member toMember = memberRepository.findById(con, toId);

        int fromMemberMoney = fromMember.getMoney();
        int toMemberMoney = toMember.getMoney();

        if (fromMemberMoney - money < 0) throw new IllegalStateException("잔액이 부족합니다.");

        memberRepository.update(con, fromId, fromMemberMoney - money);

        if (toId.equals("ex")) throw new IllegalStateException("이체중 예외 발생");

        memberRepository.update(con, toId, toMemberMoney + money);
    }

    // Transaction Logic
    public void accountTransaction(String fromId, String toId, int money) throws SQLException {

        Connection con = dataSource.getConnection();
        try {
            // Transaction 시작
            con.setAutoCommit(false);

            // Business logic
            buzLogic(fromId, toId, money, con);

            // Transaction 성공 종료 - commit
            con.commit();

        } catch (Exception e) {
            // Transaction 실패 종료 - rollback
            con.rollback();
            throw new IllegalStateException(e);
        }finally {
            if (con != null) {
                try {
                    // 다음 사람을 위해 자동 Commit 으로 다시 바꿔줘야 한다.
                    con.setAutoCommit(true);
                    // Connection 종료
                    con.close();
                } catch (Exception e) {
                    log.info("error", e);
                }
            }
        }

    }
}
```

<br>

### 📍 Test

- 이체중 예외가 발생하면 rollback 이 되기 때문에 초기에 설정해둔 10000 으로 돌아가게 된다.

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV1;
import hello.jdbc.repository.MemberRepositoryV2;
import org.junit.jupiter.api.*;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import java.sql.SQLException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

/**
 * Transaction - Connection Param 전달 방식 동기화
 */
class MemberServiceV1Test {

    public static final String Member_A = "memberA";
    public static final String Member_B = "memberB";
    public static final String Member_EX = "ex";

    private MemberRepositoryV2 memberRepository;
    private MemberServiceV2 memberService;

    @BeforeEach
    void beforeEach() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        memberRepository = new MemberRepositoryV2(dataSource);
        memberService = new MemberServiceV2(dataSource, memberRepository);
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

### 📍 문제점

- application 에서 Transaction 을 적용하려면 Service 계층이 매우 지저분해진다.
- Connection 을 유지하도록 로직을 만드는 과정도 길고 어렵다.