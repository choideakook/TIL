# JDBC Template 반복 문제 해결

JDBC 코드는 Connection 을 생성하고 Parameter 바인딩, 예외처리, 연결 종료같이 단순 반복되는 코드가 너무 지저분하고 많은 부분을 차지한다.

이러한 단순 반복 문제를 JDBC Template 이 상당부분 줄여줄 수 있다.

## ✏️ JDBC Template 적용하기

- 모든 DI 를 없애고 JdbcTemplate 하나로 통일 시킨다.
    - JdbcTemplate 이 모든 기능을 대신할 수 있다.
- 기존 코드의 핵심 로직인 SQL 문만 남겨놓고 모두 제거한다.
    - 그동안 코드로 힘들게 작성했던 반복되는 코드는 JdbcTemplate 가 알아서 처리해줄수 있다.
- JdbcTemplate 의 method 를 사용해 parameter 값을 전달해준다.
    - find by id 같은 조회 로직은 RowMapper 가 필요하다.
    - 조회로직은 응답으로 요청한 정보를 조회해야 하는 기능인데,
    응답값 result set 을 RowMapper 에 담아서 반환해준다.
    - RowMapper 를 통해 반환된 값을 변수값으로 정할 수  있게된다.
- 기존에 만들었던 Connection 동기화와 close 로직도 JdbcTemplate 가 대신한다.

```java
package hello.jdbc.repository;

import hello.jdbc.domain.Member;
import lombok.extern.slf4j.Slf4j;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import javax.sql.DataSource;

/**
 * JDBC Template 사용
 */
@Slf4j
public class MemberRepositoryV5 implements MemberRepository {

    // 기존에 사용했던 의존관계들을 모두 JdbcTemplate 하나로 해결할 수 있다.
    private final JdbcTemplate template;

    public MemberRepositoryV5(DataSource dataSource) {
        this.template = new JdbcTemplate(dataSource);
    }

    @Override
    public Member save(Member member){
        String sql = "insert into member(member_id, money) values(?, ?)";

        // sql 문을 첫번째 param 으로 하고 그 다음부터는 SQL 의 param 값을 넣어주면 된다.
        // template 이 커넥션 연결과 종료, 예외 변환, 예외 throw 등
        // JDBC 로 구연했던 모든 기능을 대신할 수 있다.
        template.update(sql, member.getMemberId(), member.getMoney());
        return member;
    }

    @Override
    public Member findById(String memberId){
        String sql = "select * from member where member_id = ?";

        // data 1 건을 조회하는 method
        return template.queryForObject(sql, memberRowMapper(), memberId);
    }

    // DB 의 응답 값을 RowMapper 가 받아서 return 값으로 변수에 입력해줌
    private RowMapper<Member> memberRowMapper() {
        // rs = result set (DB 의 응답 값)
        return (rs, rowNum)-> {
            Member member = new Member();
            member.setMemberId(rs.getString("member_id"));
            member.setMoney(rs.getInt("money"));
            return member;
        };
    }

    @Override
    public void update(String memberId, int money){
        String sql = "update member set money=? where member_id=?";
        // SQL parameter 순서를 잘 지켜서 입력해주어야 함 
        template.update(sql, money, memberId);
    }

    @Override
    public void delete(String memberId){
        String sql = "delete from member where member_id=?";

        template.update(sql, memberId);
    }

    // Connection 동기화와 종료도 별도로 할 필요없이 Template 에서 전부 처리해준다.
}
```

<br>

### 📍 Test

repository 의 구현체를 V5 로 바꿔주면 새로운 로직을 적용할 수 있다.

- 정상적으로 Test 가 작동된다.

```java
@TestConfiguration
    static class TestConfig {

        private final DataSource dataSource;

        public TestConfig(DataSource dataSource) {
            this.dataSource = dataSource;
        }

        @Bean
        MemberRepository memberRepository() {
//            return new MemberRepositoryV4_2(dataSource);
            return new MemberRepositoryV5(dataSource);
        }

        @Bean
        MemberServiceV4 memberService() {
            return new MemberServiceV4(memberRepository());
        }
    }
```