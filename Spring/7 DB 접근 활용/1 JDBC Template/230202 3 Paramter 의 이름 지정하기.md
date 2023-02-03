# Paramter 의 이름 지정하기

## ✏️ NamedParameterJdbcTemplate

### 📍 필요성

- SQL 문의 ? 를 바인딩 하기 위해서 update 의 Param 값을 순서대로 작성해주어야한다.
    - 이 방식은 SQL 문을 수정할 때 순서를 실수할 확률 이 있고,
    지금 상황에서 재고와 가격이 바뀌면 치명적인 문제가 발생하게 된다.
- 개발자들이 중요하게 생각해야 하는건 코드를 몇줄 줄이는것이 아닌,
모호함을 코드에서 제거해 명확하게 만드는 것이다.

```java
   @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        String sql = "update item set item_name=?, price=?, quantity=? where id=?";
        template.update(sql,
                updateParam.getItemName(),
                updateParam.getPrice(),
                updateParam.getQuantity(),
                itemId);
    }
```

<br>

### 📍 Parameter 이름을 지정해서 문제 해결

NamedParameterJdbcTemplate 을 이용해 Parameter 이름을 지정할 수 있다.

- NamedParameterJdbcTemplate
    - SqlParameterSource
        - 방법 1. BeanPropertySqlParameterSource
        - 방법 2. MapSqlParameterSource
    - 방법 3. Map
- BeanPropertyRowMapper
    - Item 의 필드값을 이용해 result set 을 받는 기능

```java
package hello.itemservice.repository.jdbcTemplate;

import hello.itemservice.domain.Item;
import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.ItemSearchCond;
import hello.itemservice.repository.ItemUpdateDto;
import lombok.extern.slf4j.Slf4j;
import org.springframework.dao.EmptyResultDataAccessException;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.BeanPropertySqlParameterSource;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate;
import org.springframework.jdbc.core.namedparam.SqlParameterSource;
import org.springframework.jdbc.support.GeneratedKeyHolder;
import org.springframework.jdbc.support.KeyHolder;
import org.springframework.stereotype.Repository;
import org.springframework.util.StringUtils;

import javax.sql.DataSource;
import java.util.List;
import java.util.Map;
import java.util.Optional;

/**
 * NamedParameterJdbcTemplate JDBC Template
 * SqlParameterSource
 * - BeanPropertySqlParameterSource
 * - MapSqlParameterSource
 * Map
 *
 * BeanPropertyRowMapper
 */
@Slf4j
@Repository
public class JdbcTemplateItemRepositoryV2 implements ItemRepository {

//    private final JdbcTemplate template;
    private final NamedParameterJdbcTemplate template;

    // 마찬가지로 DataSource 가 필요하다.
    public JdbcTemplateItemRepositoryV2(DataSource dataSource) {
        this.template = new NamedParameterJdbcTemplate(dataSource);
    }

    @Override
    public Item save(Item item) {
        // Param 을 ? 가 아닌 이름 기반으로 변경해주어야 한다.
        String sql = "insert into item (item_name, price, quantity) " +
                "values (:itemName, :price, :quantity)";

        // 방법 1.
        // item class 의 필드명을 기반으로 parameter 값을 바인딩하게 된다.
        SqlParameterSource param = new BeanPropertySqlParameterSource(item);

        KeyHolder keyHolder = new GeneratedKeyHolder();
        template.update(sql, param, keyHolder);

        // 생성된 key 값을 item 에 set 해줌
        long key = keyHolder.getKey().longValue();
        item.setId(key);

        return item;
    }

    @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        String sql = "update item " +
                "set item_name=:itemName, price=:price, quantity=:quantity " +
                "where id=:id";

        // 방법 2.
        // 직접 바인딩이 필요한 값들을 하나하나 이름과 함께 지정해주는 방법
        SqlParameterSource param = new MapSqlParameterSource()
                .addValue("itemName", updateParam.getItemName())
                .addValue("price", updateParam.getPrice())
                .addValue("quantity", updateParam.getQuantity())
                .addValue("id", itemId);
        template.update(sql, param);
    }

    @Override
    public Optional<Item> findById(Long id) {
        String sql = "select id, item_name, price, quantity from item where id=:id";

        try {
            // 방법 3.
            // map 을 사용해 바인딩 하는 방법
            Map<String, Long> param = Map.of("id", id);
            Item item = template.queryForObject(sql, param, itemLowMapper());
            return Optional.of(item);
        } catch (EmptyResultDataAccessException e) {

            return Optional.empty();
        }
    }

    @Override
    public List<Item> findAll(ItemSearchCond cond) {
        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        // 방법 1.
        // ItemSearchCond Class 의 필드값을 이용해 바인딩하는 방법
        SqlParameterSource param = new BeanPropertySqlParameterSource(cond);

        String sql = "select id, item_name, price, quantity from item";

        // 동적 쿼리
        if (StringUtils.hasText(itemName) || maxPrice != null) {
            sql += " where";
        }
        boolean andFlag = false;
        if (StringUtils.hasText(itemName)) {
            sql += " item_name like concat('%',:itemName,'%')";
            andFlag = true;
        }
        if (maxPrice != null) {
            if (andFlag) {
                sql += " and";
            }
            sql += " price <= :maxPrice";
        }
        log.info("sql={}", sql);

        return template.query(sql, param, itemLowMapper());
    }

    private RowMapper<Item> itemLowMapper() {

        // row mapper 로 받아야 하는 result set 이 item class 의 필드값과 동일하기 때문에
        // BeanPropertyRowMapper 을 이용해 편하게 응답값을 받아올 수 있다.
        return BeanPropertyRowMapper.newInstance(Item.class);

//        return ((rs, rowNum) -> {
//            Item item = new Item();
//            item.setId(rs.getLong("id"));
//            item.setItemName(rs.getString("item_name"));
//            item.setPrice(rs.getInt("price"));
//            item.setQuantity(rs.getInt("quantity"));
//            return item;
//        });
    }
}
```

<br>

### ❗ 방법 1. BeanPropertySqlParameterSource 를 항상 사용할 수 없는이유

방법 1. 은 Class 의 필드값을 기반으로 이름과 맞는 param 를 바인딩해주는 기능이다.

한줄로 모든 param 을 자동으로 맞춰주기 때문에 편리하게 사용할 수 있지만 어느때나 사용할 수 있는 기능은 아니다.

- update 를 보면 parameter 가 item id 와 dto 이고,
필요한 param 은 id 를 포함한 4가지이다.

```java
   @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        String sql = "update item " +
                "set item_name=:itemName, price=:price, quantity=:quantity " +
                "where id=:id";

        // 방법 2.
        // 직접 바인딩이 필요한 값들을 하나하나 이름과 함께 지정해주는 방법
        SqlParameterSource param = new MapSqlParameterSource()
                .addValue("itemName", updateParam.getItemName())
                .addValue("price", updateParam.getPrice())
                .addValue("quantity", updateParam.getQuantity())
                .addValue("id", itemId);
        template.update(sql, param);
    }
```

- DTO 에는 id 가 지정되어있지 않기 때문에 BeanPropertySqlParameterSource 를 사용할 경우 id 가 바인딩되지 않는 문제가 생긴다.

## ✏️ 적용해서 실행해보기

Config 를 V2 버전으로 생성하고

```java
package hello.itemservice.config;

import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.jdbcTemplate.JdbcTemplateItemRepositoryV2;
import hello.itemservice.service.ItemService;
import hello.itemservice.service.ItemServiceV1;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
@RequiredArgsConstructor
public class JdbcTemplateV2Config {

    private final DataSource dataSource;

    @Bean
    public ItemService itemService() {
        return new ItemServiceV1(itemRepository());
    }

    @Bean
    public ItemRepository itemRepository() {
        return new JdbcTemplateItemRepositoryV2(dataSource);

    }
}
```

import 도 v2 버전으로 바꿔준 후 application 을 실행하면 정상적으로 반영되어 작동한다.

```java
package hello.itemservice;

import hello.itemservice.config.*;
import hello.itemservice.repository.ItemRepository;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.Profile;

// 컴포넌트 스캔 범위 지정
//@Import(MemoryConfig.class)
//@Import(JdbcTemplateV1Config.class)
@Import(JdbcTemplateV2Config.class)
@SpringBootApplication(scanBasePackages = "hello.itemservice.web")
public class ItemServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ItemServiceApplication.class, args);
	}

	@Bean
	@Profile("local")
	public TestDataInit testDataInit(ItemRepository itemRepository) {
		return new TestDataInit(itemRepository);
	}

}
```