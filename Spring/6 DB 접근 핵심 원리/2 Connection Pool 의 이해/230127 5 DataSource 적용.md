# DataSource 적용

## ✏️ MemberRepositoryV1

1. DataSource 를 주입받기 위한 DI 생성
2. dataSource 로 부터 Connection 을 획득하도록 로직 변경
3. close method 방식을 JdbcUtils 로 변경
4. 나머지 CRUD 로직은 그대로 두면 된다.

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
 * JDBC - DataSource , JDBCUtils 사용
 */
@Slf4j
public class MemberRepositoryV1 {

    // DataSource 를 주입받기 위한 DI 생성
    private final DataSource dataSource;

    public MemberRepositoryV1(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    public Member save(Member member) throws SQLException {
        String sql = "insert into member(member_id, money) values(?, ?)";
        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, member.getMemberId());
            pstmt.setInt(2, member.getMoney());
            pstmt.executeUpdate();
            return member;

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        }finally {
            close(con, pstmt, null);
        }
    }

    public Member findById(String memberId) throws SQLException {
        String sql = "select * from member where member_id = ?";
        Connection con = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;

        try {
            con = DBConnectionUtil.getConnection();
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
            close(con, pstmt, rs);
        }
    }

    public void update(String memberId, int money) throws SQLException {
        String sql = "update member set money=? where member_id=?";
        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setInt(1, money);
            pstmt.setString(2, memberId);

            int resultSize = pstmt.executeUpdate();
            log.info("resultSize={}", resultSize);

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        }finally {
            close(con, pstmt, null);
        }
    }

    public void delete(String memberId) throws SQLException {
        String sql = "delete from member where member_id=?";
        Connection con = null;
        PreparedStatement pstmt = null;

        try {
            con = getConnection();
            pstmt = con.prepareStatement(sql);
            pstmt.setString(1, memberId);
            pstmt.executeUpdate();

        } catch (SQLException e) {
            log.error("db error", e);
            throw e;
        } finally {
            close(con, pstmt, null);
        }

    }

    // close method 방식을 JdbcUtils 로 변경
    private void close(Connection con, Statement stmt, ResultSet rs) {
        JdbcUtils.closeResultSet(rs);
        JdbcUtils.closeStatement(stmt);
        JdbcUtils.closeConnection(con);
    }

    // dataSource 로 부터 Connection 을 획득하도록 변경
    private Connection getConnection() throws SQLException {
        Connection con = dataSource.getConnection();
        log.info("get Connection = {} , class = {}", con, con.getClass());
        return con;
    }
}
```

<br>

### ✏️ Test

### 📍 Driver Manager DataSource 를 통한 Connection 획득

```java
package hello.jdbc.repository;

import hello.jdbc.domain.Member;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import java.sql.SQLException;
import java.util.NoSuchElementException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

@Slf4j
class MemberRepositoryV1Test {

    MemberRepositoryV1 repository;

    // Test 가 실행하기전에 먼저 실행하게 되는 어노테이션
    @BeforeEach
    void beforeEach() {
        // Driver Manager DataSource 를 통한 새로운 Connection 획득
        DriverManagerDataSource dataSource = new DriverManagerDataSource(URL, USERNAME, PASSWORD);
        repository = new MemberRepositoryV1(dataSource);
    }

    @Test
    void crud() throws SQLException {
        // save
        Member member = new Member("memberV100", 10000);
        repository.save(member);

        // find by id
        Member findMember = repository.findById(member.getMemberId());
        log.info("findMember = {}", findMember);
        assertThat(findMember).isEqualTo(member);

        // update : money - 10000 -> 20000
        repository.update(member.getMemberId(), 20000);
        Member updatedMember = repository.findById(member.getMemberId());
        assertThat(updatedMember.getMoney()).isEqualTo(20000);

        //delete
        repository.delete(member.getMemberId());
        NoSuchElementException e = assertThrows(NoSuchElementException.class,
                () -> repository.findById(member.getMemberId()));
    }

}
```

### 🔍 출력물 확인

- get connection=conn 을 보면 커넥션을 맺을 때 마다 숫자가 증가하며 새로 맺고 있다는걸 확인할 수 있다.
- connection 이 필요할 때 마다 새로 connection 을 획득하기 때문에 성능이 낮아질 수 밖에 없다.

```java
20:45:08.910 [main] DEBUG org.springframework.jdbc.datasource.DriverManagerDataSource - Creating new JDBC DriverManager Connection to [jdbc:h2:tcp://localhost/~/jdbc]
20:45:08.960 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = {} , class = {}
20:45:08.967 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn1: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
20:45:08.969 [main] INFO hello.jdbc.repository.MemberRepositoryV1Test - findMember = Member(memberId=memberV100, money=10000)
20:45:08.995 [main] DEBUG org.springframework.jdbc.datasource.DriverManagerDataSource - Creating new JDBC DriverManager Connection to [jdbc:h2:tcp://localhost/~/jdbc]
20:45:08.997 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = {} , class = {}
20:45:08.998 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - resultSize=1
20:45:08.999 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn3: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
20:45:09.001 [main] DEBUG org.springframework.jdbc.datasource.DriverManagerDataSource - Creating new JDBC DriverManager Connection to [jdbc:h2:tcp://localhost/~/jdbc]
20:45:09.002 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = {} , class = {}
20:45:09.004 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn5: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
```

<br>

### 📍 connection pool 을 통한 connection 획득
HikariCP

- 동일한 코드에 BeforeEach 만 connection pool 로 수정해주면 된다.

```java
package hello.jdbc.repository;

import com.zaxxer.hikari.HikariDataSource;
import hello.jdbc.domain.Member;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

import java.sql.SQLException;
import java.util.NoSuchElementException;

import static hello.jdbc.connection.ConnectionConst.*;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

@Slf4j
class MemberRepositoryV1Test {

    MemberRepositoryV1 repository;

    @BeforeEach
    void beforeEach() {
     
        // connection pooling
        // 따로 설정 안해준 부분은 기본값으로 설정됨
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setJdbcUrl(URL);
        dataSource.setUsername(USERNAME);
        dataSource.setPassword(PASSWORD);
        repository = new MemberRepositoryV1(dataSource);
    }

    @Test
    void crud() throws SQLException, InterruptedException {

        ...

        // connection pool 생성 로그를 보기위한 인위적인 지연
        Thread.sleep(1000);
    }

}
```

### 🔍 출력물 확인

- get connection 에 wrapping conn 을 확인해보면 이전과 다르게 모두 동일한 0 번인걸 확인할 수 있다.
    - connection pool 에서 Close() 는 연결 종료가 아닌 connection 반환이기 때문에 사용이 끝나면 pool 로 반환하고 다시 필요하면 pool 에서 사용했기 때문이다.
- 마지막 After adding stats 을 보면 active 는 0 이고 idle 이 10 인 이유도 커넥션을 전부 반납했기 때문이다.

```java
21:57:13.466 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = HikariProxyConnection@485845532 wrapping conn0: url=jdbc:h2:tcp://localhost/~/jdbc user=SA , class = class com.zaxxer.hikari.pool.HikariProxyConnection
21:57:13.483 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn1: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
21:57:13.486 [main] INFO hello.jdbc.repository.MemberRepositoryV1Test - findMember = Member(memberId=memberV100, money=10000)
21:57:13.515 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = HikariProxyConnection@507819576 wrapping conn0: url=jdbc:h2:tcp://localhost/~/jdbc user=SA , class = class com.zaxxer.hikari.pool.HikariProxyConnection
21:57:13.516 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - resultSize=1
21:57:13.517 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn2: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
21:57:13.519 [main] INFO hello.jdbc.repository.MemberRepositoryV1 - get Connection = HikariProxyConnection@2108297149 wrapping conn0: url=jdbc:h2:tcp://localhost/~/jdbc user=SA , class = class com.zaxxer.hikari.pool.HikariProxyConnection
21:57:13.522 [main] INFO hello.jdbc.connection.DBConnectionUtil - get connection=conn3: url=jdbc:h2:tcp://localhost/~/jdbc user=SA, class=class org.h2.jdbc.JdbcConnection
21:57:13.569 [HikariPool-1 housekeeper] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Pool stats (total=1, active=0, idle=1, waiting=0)
21:57:13.571 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn4: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.573 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn5: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.574 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn6: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.575 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn7: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.577 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn8: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.578 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn9: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.579 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn10: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.581 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn11: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.582 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - Added connection conn12: url=jdbc:h2:tcp://localhost/~/jdbc user=SA
21:57:13.582 [HikariPool-1 connection adder] DEBUG com.zaxxer.hikari.pool.HikariPool - HikariPool-1 - After adding stats (total=10, active=0, idle=10, waiting=0)
```