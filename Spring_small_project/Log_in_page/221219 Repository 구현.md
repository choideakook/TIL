# Repository 구현

## ✏️ 1.  사용자가 회원가입을 위해 입력해야 하는 data 만들기

- pk : id
- user id / pw / name
    
    💡get / set 방식으로 만들었다.
    

```java
package login.loginspring.domain;

public class Member {

    private Long id;
    private String userId , Pw , name ;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUserId() {
        return userId;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public String getPw() {
        return Pw;
    }

    public void setPw(String pw) {
        Pw = pw;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

## ✏️ 2.  기능을 구현 할 Interface 생성

- 총 5가지 기능을 구현할 예정이다.
    - 사용자가 입력한 값을 저장하는 기능 save
    - 저장된 값을 탐색하는 기능 findBy ~ (null 을 방지하기위해 Optional 을 사용했다.)
    - 저장된 모든 값을 가져오는 findAll

```java
package login.loginspring.repository;

import login.loginspring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {

    Member save (Member member);
    Optional<Member> findById (String userId);
    Optional<Member> findByPw (String pw);
    Optional<Member> findByName (String name);
    List<Member> findAll ();
}
```

## ✏️ 3. 구현체 제작 (JDBC Template 와 H2 DB 를 사용했다.)

- JdbcTemplate DI
- Row Mapper
- 구체적인 기능 제작

```java
package login.loginspring.repository;

import login.loginspring.domain.Member;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;

import javax.sql.DataSource;
import java.util.*;

public class JdbcTemplateRepository implements MemberRepository{

    // jdbc template 생성
    private final JdbcTemplate jtmp;

    @Autowired
    public JdbcTemplateRepository (DataSource source){
        jtmp = new JdbcTemplate(source);
    }

    @Override
    public Member save(Member member) {
        // simple jdbc insert 생성
        // db 에 input 하기 전 자체적으로 임시 db 를 생성하고 관리하는 Class
        SimpleJdbcInsert insert = new SimpleJdbcInsert(jtmp);

        // insert 에 table 명과 pk 명을 입력해줌
        insert.withTableName("member")
                .usingGeneratedKeyColumns("id");

        // 별도의 query 문 없이 parameter 들을 map 에 저장하면
        // key 값에 해당하는 column 명에 value 값을 삽입한다.
        Map<String, String> params = new HashMap<>();
        params.put("userId", member.getUserId());
        params.put("pw", member.getPw());
        params.put("name", member.getName());

        // executeAndReturnKey 는 자동으로 pk 에 auto_increment 를 반환해준다.
        Number key = insert.executeAndReturnKey(
                new MapSqlParameterSource(params)
        );

        member.setId(key.longValue());
        return member;
    }

    @Override
    public Optional<Member> findById(String userId) {
        List<Member> result = jtmp.query(
                "select * from member where id =?", memberRowMapper(), userId
        );
        return result.stream().findAny();
    }

    @Override
    public Optional<Member> findByPw(String pw) {
        List<Member> result = jtmp.query(
                "select * from member where pw =?", memberRowMapper(), pw
        );
        return result.stream().findAny();
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jtmp.query(
                "select * from member where name =?", memberRowMapper(), name
        );
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return jtmp.query("select * from member", memberRowMapper());
    }

    private RowMapper<Member> memberRowMapper (){
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setName(rs.getString("name"));
            member.setPw(rs.getString("pw"));
            member.setUserId(rs.getString("userId"));
            return member;
        };
    }
}
```

## ✏️ 4. Test case

- Save 기능

```java
@Autowired
    MemberRepository repository;

    @Test
    void save() {
        Member member = new Member();
        member.setName("최대국");
        member.setUserId("shdrnrhd112");
        member.setPw("m123123");

        repository.save(member);

        Member result = repository.findById(member.getUserId()).get();
        assertThat(member).isEqualTo(result);
```

❗️에러가 발생했다 ㅜㅜ

```java
Error creating bean with name 
	'login.loginspring.repository.JdbcTemplateRepositoryTest'
	: Unsatisfied dependency expressed through field 'repository';
```

아직 에러 보는 법을 잘 모르지만 느낌상 이 부분이 핵심인것같다..