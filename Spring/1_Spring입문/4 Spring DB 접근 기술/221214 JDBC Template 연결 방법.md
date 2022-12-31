# JDBC Template 연결 방법

### 🚨 jdbc template 매뉴얼을 확인하면 더 자세한 정보를 확인할 수 있음!

## ✏️ 0. JDBC Template 의 특징

- 순수 jdbc 의 반복되는 코드를 대부분 제거해준다.
- SQL 은 직접 작성해야 한다.

## ✏️ 1. 환경설정

build.gradle 파일에 jdbc 와 DB 관련 라이브 러리를 추가해야됨 (h2 연결하는법을 할 예정)

EX)

- build.gradle 파일의 `dependencies` 에서 두가지 코드를 추가해줌

```
📍 jdbc
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
📍 h2 database 라이브러리
runtimeOnly 'com.h2database:h2'
```

- DB 경로와 드라이버의 이름, user name 을 입력해준다.

(경로 : src - main - resources - [application.properties](http://application.properties))

```
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```

## ✏️ 2 . 인터페이스 구현 준비

Member Repository 의 인터페이스를 구현하기 위해서 이 class 를 implements 하는 class 를 생성함 (JdbcTemplateMemberRepository)

💡단축키  O + E

```java
public class JdbcTemplateMemberRepository implements MemberRepository{

    @Override
    public Member save(Member member) {
        return null;
    }

    @Override
    public Optional<Member> findById(Long id) {
        return Optional.empty();
    }

    @Override
    public Optional<Member> findByName(String name) {
        return Optional.empty();
    }

    @Override
    public List<Member> findAll() {
        return null;
    }
}
```

## ✏️ 3. 의존관계 형성

※ jdbc template 와 의존성 주입을 하기위해 필요한 변수 JdbcTemplate 의 코드를 작성할껀데 이 class 는 생성자 주입 방식으로 injection 이 되지않아 DataSource 를 사용한다.

```java
private final JdbcTemplate jdbcTemplate;

		// jdbc template 에 data source 를 주입해서 사용
    @Autowired
    public JdbcTemplateMemberRepository(DataSource dataSource) {
        jdbcTemplate = new JdbcTemplate(dataSource);
    }
```

## ✏️ 4. Row Mapper 생성

Data Base 의 객체들을 java 로 가져오기위해 Row Mapper 를 이용할 수 있으며

원시적인 방식으로 연결할때 똑같은 코드를 반복했던 중복을 없앨 수 있다.

row mapper 로 db 의 모든 column  값을 가져와 member 에 set 해줌

```java
private RowMapper<Member> memberRowMapper() {
        return new RowMapper<Member>() {

            @Override
            public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return member;
            }
        };
    }
```

리팩토링을 위해 람다 코드로 변형함

(단축키 O + E → replace with lamda)

```java
private RowMapper<Member> memberRowMapper() {
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setId(rs.getLong("id"));
            member.setName(rs.getString("name"));
            return member;
        };
    }
```

[Row Mapper 의 사용 이유와 역할](https://velog.io/@seculoper235/RowMapper에-대해)

## ✏️ 5.  구체적 인터페이스 구현

### 1. Save

이유는 모르겠지만 jdbcTemplate.query 문을 사용하지 않고

Simple Jdbc Inset 라는 class 를 사용해 새로운 row 를 insert 시켰다.

```java
@Override
    public Member save(Member member) {
        // SQL query 를 대체 하는 코드 (jdbcTemplate.query 대체)
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withTableName("member")
                .usingGeneratedKeyColumns("id");

        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());

        // parameters 에서 나온 key 값을 Numbeer key 변수에 넣어줌
        Number key = jdbcInsert.executeAndReturnKey(
                new MapSqlParameterSource(parameters)
        );
        // key 값을 member id 에 set 해줌
        member.setId(key.longValue());
        return member;
    }
```

### 2. Find by Id 와 Find by name

💡find by id

sql 에서 id 값을 찾기위해 where id = ? 로 첫번째 parameter 값을 주고

두번제 parameter 값은 row mapper 를 넣어

row mapper 에서 가져온 id 값이 사용자가 입력한 id 값과 일치하는지 확인한다.

```java
@Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query(
                "select * from member where id =?", memberRowMapper(), id
        );
        return result.stream().findAny();
    }
```

💡find by name

앞의 id 와 동일한 방식으로 where 절의 id 를 name 으로 바꿔주면 완료!

```java
@Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query(
                "select * from member where name =?", memberRowMapper(), name
        );
        return result.stream().findAny();
    }
```

[SQL 언어 에서 ? (Place Holder) 의 역할](https://codedragon.tistory.com/10320)

### 3. find all

위의 find by name 과 id 와 동일한 방식으로 모든 정보를 가져와야 하므로 where 절을 없애준 후 List 에 넣어주는 것이 아닌 리턴값으로 해주면 된다.

```java
@Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member", memberRowMapper());
    }
```

## ✏️ 6. 새로운 repository 스프링 빈으로 Config 해주기

기존 memory member reository 를 지우고 방금 만들어진 jdbc Class 로 바꿔주면된다.

```java
@Bean
    public MemberRepository memberRepository(){

//        return new MemoryMemberRepository();
        return new JdbcTemplateMemberRepository();
    }
```

하지만 이렇게 할경우 parameter 에 data source 가 들어와 있지 않기때문에 Auto wired 로 연결 시켜줘야한다

```java
// 데이터 소스 오토 와이어드
private DataSource dataSource;
    
    @Autowired
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

// 파라메터값 에 넣어주기
@Bean
    public MemberRepository memberRepository(){

//        return new MemoryMemberRepository();
        return new JdbcTemplateMemberRepository(dataSource);
    }
```
