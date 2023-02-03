# Simple JDBC Insert

Simple JDBC Insert 를 이용해서 DB insert 를 편하게 사용할 수 있다.

```java
/**
 * Simple Jdbc Insert
 */
@Slf4j
@Repository
public class JdbcTemplateItemRepositoryV3 implements ItemRepository {

    private final NamedParameterJdbcTemplate template;
    private final SimpleJdbcInsert jdbcInsert;

    public JdbcTemplateItemRepositoryV3(DataSource dataSource) {
        this.template = new NamedParameterJdbcTemplate(dataSource);
        // 의존관계 주입을 하면서 table 이름과 generate key 를 설정해주면된다.
        this.jdbcInsert = new SimpleJdbcInsert(dataSource)
                .withTableName("item")
                .usingGeneratedKeyColumns("id");
                // column 이름은 생략 가능
//                .usingColumns("item_name", "price", "quantity");
    }

    @Override
    public Item save(Item item) {
        SqlParameterSource param = new BeanPropertySqlParameterSource(item);
        Number key = jdbcInsert.executeAndReturnKey(param);
        item.setId(key.longValue());
        return item;
    }
```

Config 를 변경하고 application 을 실행하면 정상적으로 insert 가 작동된다.