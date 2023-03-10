# JDBC Template μ°κ²° λ°©λ²

### π¨Β jdbc template λ§€λ΄μΌμ νμΈνλ©΄ λ μμΈν μ λ³΄λ₯Ό νμΈν  μ μμ!

## βοΈΒ 0. JDBC Template μ νΉμ§

- μμ jdbc μ λ°λ³΅λλ μ½λλ₯Ό λλΆλΆ μ κ±°ν΄μ€λ€.
- SQL μ μ§μ  μμ±ν΄μΌ νλ€.

## βοΈΒ 1. νκ²½μ€μ 

build.gradle νμΌμ jdbc μ DB κ΄λ ¨ λΌμ΄λΈ λ¬λ¦¬λ₯Ό μΆκ°ν΄μΌλ¨ (h2 μ°κ²°νλλ²μ ν  μμ )

EX)

- build.gradle νμΌμ `dependencies` μμ λκ°μ§ μ½λλ₯Ό μΆκ°ν΄μ€

```
π jdbc
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
π h2 database λΌμ΄λΈλ¬λ¦¬
runtimeOnly 'com.h2database:h2'
```

- DB κ²½λ‘μ λλΌμ΄λ²μ μ΄λ¦, user name μ μλ ₯ν΄μ€λ€.

(κ²½λ‘ : src - main - resources - [application.properties](http://application.properties))

```
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
```

## βοΈΒ 2 . μΈν°νμ΄μ€ κ΅¬ν μ€λΉ

Member Repository μ μΈν°νμ΄μ€λ₯Ό κ΅¬ννκΈ° μν΄μ μ΄ class λ₯Ό implements νλ class λ₯Ό μμ±ν¨ (JdbcTemplateMemberRepository)

π‘λ¨μΆν€  O + E

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

## βοΈΒ 3. μμ‘΄κ΄κ³ νμ±

β» jdbc template μ μμ‘΄μ± μ£Όμμ νκΈ°μν΄ νμν λ³μ JdbcTemplate μ μ½λλ₯Ό μμ±ν κ»λ° μ΄ class λ μμ±μ μ£Όμ λ°©μμΌλ‘ injection μ΄ λμ§μμ DataSource λ₯Ό μ¬μ©νλ€.

```java
private final JdbcTemplate jdbcTemplate;

		// jdbc template μ data source λ₯Ό μ£Όμν΄μ μ¬μ©
    @Autowired
    public JdbcTemplateMemberRepository(DataSource dataSource) {
        jdbcTemplate = new JdbcTemplate(dataSource);
    }
```

## βοΈΒ 4. Row Mapper μμ±

Data Base μ κ°μ²΄λ€μ java λ‘ κ°μ Έμ€κΈ°μν΄ Row Mapper λ₯Ό μ΄μ©ν  μ μμΌλ©°

μμμ μΈ λ°©μμΌλ‘ μ°κ²°ν λ λκ°μ μ½λλ₯Ό λ°λ³΅νλ μ€λ³΅μ μμ¨ μ μλ€.

row mapper λ‘ db μ λͺ¨λ  column  κ°μ κ°μ Έμ member μ set ν΄μ€

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

λ¦¬ν©ν λ§μ μν΄ λλ€ μ½λλ‘ λ³νν¨

(λ¨μΆν€ O + E β replace with lamda)

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

[Row Mapper μ μ¬μ© μ΄μ μ μ­ν ](https://velog.io/@seculoper235/RowMapperμ-λν΄)

## βοΈΒ 5.  κ΅¬μ²΄μ  μΈν°νμ΄μ€ κ΅¬ν

### 1. Save

μ΄μ λ λͺ¨λ₯΄κ² μ§λ§ jdbcTemplate.query λ¬Έμ μ¬μ©νμ§ μκ³ 

Simple Jdbc Inset λΌλ class λ₯Ό μ¬μ©ν΄ μλ‘μ΄ row λ₯Ό insert μμΌ°λ€.

```java
@Override
    public Member save(Member member) {
        // SQL query λ₯Ό λμ²΄ νλ μ½λ (jdbcTemplate.query λμ²΄)
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withTableName("member")
                .usingGeneratedKeyColumns("id");

        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());

        // parameters μμ λμ¨ key κ°μ Numbeer key λ³μμ λ£μ΄μ€
        Number key = jdbcInsert.executeAndReturnKey(
                new MapSqlParameterSource(parameters)
        );
        // key κ°μ member id μ set ν΄μ€
        member.setId(key.longValue());
        return member;
    }
```

### 2. Find by Id μ Find by name

π‘find by id

sql μμ id κ°μ μ°ΎκΈ°μν΄ where id = ? λ‘ μ²«λ²μ§Έ parameter κ°μ μ£Όκ³ 

λλ²μ  parameter κ°μ row mapper λ₯Ό λ£μ΄

row mapper μμ κ°μ Έμ¨ id κ°μ΄ μ¬μ©μκ° μλ ₯ν id κ°κ³Ό μΌμΉνλμ§ νμΈνλ€.

```java
@Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query(
                "select * from member where id =?", memberRowMapper(), id
        );
        return result.stream().findAny();
    }
```

π‘find by name

μμ id μ λμΌν λ°©μμΌλ‘ where μ μ id λ₯Ό name μΌλ‘ λ°κΏμ£Όλ©΄ μλ£!

```java
@Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query(
                "select * from member where name =?", memberRowMapper(), name
        );
        return result.stream().findAny();
    }
```

[SQL μΈμ΄ μμ ? (Place Holder) μ μ­ν ](https://codedragon.tistory.com/10320)

### 3. find all

μμ find by name κ³Ό id μ λμΌν λ°©μμΌλ‘ λͺ¨λ  μ λ³΄λ₯Ό κ°μ ΈμμΌ νλ―λ‘ where μ μ μμ μ€ ν List μ λ£μ΄μ£Όλ κ²μ΄ μλ λ¦¬ν΄κ°μΌλ‘ ν΄μ£Όλ©΄ λλ€.

```java
@Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member", memberRowMapper());
    }
```

## βοΈΒ 6. μλ‘μ΄ repository μ€νλ§ λΉμΌλ‘ Config ν΄μ£ΌκΈ°

κΈ°μ‘΄ memory member reository λ₯Ό μ§μ°κ³  λ°©κΈ λ§λ€μ΄μ§ jdbc Class λ‘ λ°κΏμ£Όλ©΄λλ€.

```java
@Bean
    public MemberRepository memberRepository(){

//        return new MemoryMemberRepository();
        return new JdbcTemplateMemberRepository();
    }
```

νμ§λ§ μ΄λ κ² ν κ²½μ° parameter μ data source κ° λ€μ΄μ μμ§ μκΈ°λλ¬Έμ Auto wired λ‘ μ°κ²° μμΌμ€μΌνλ€

```java
// λ°μ΄ν° μμ€ μ€ν  μμ΄μ΄λ
private DataSource dataSource;
    
    @Autowired
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

// νλΌλ©ν°κ° μ λ£μ΄μ£ΌκΈ°
@Bean
    public MemberRepository memberRepository(){

//        return new MemoryMemberRepository();
        return new JdbcTemplateMemberRepository(dataSource);
    }
```
