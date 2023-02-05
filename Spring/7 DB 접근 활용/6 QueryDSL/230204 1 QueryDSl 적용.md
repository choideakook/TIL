# QueryDSl 적용

## ✏️ Repository

- Query dsl 의 기본 폼
    - 괄호안에 원하는 객체를 넣으면 JPQL 로 변환된다.

```java
    @Override
    public List<Item> findAll(ItemSearchCond cond) {

        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        // 환경설정 할 때 생성한 QItem 에서 Entity 를 가져온다.
        // QItem 은 Static Class 이므로 QItem 도 import 해서 생략이 가능하다.
        List<Item> result = query.select(QItem.item)
                .from(QItem.item)
                .where()
                .fetch();

        return result;
    }
```

### 📍 동적 쿼리

- java 코드는 부등호 표시가 불가능해서 문자로 대체해서 사용된다.
    - == (eq : Equal)
    - ≠ (ne : Not Equal)
    - < (lt : Less Than)
    - '>' (gt : Greater Than)
    - ≥ (goe : Greater Than Or Equal)
    - ≤ (loe : Less Than Or Equal)

```java
package hello.itemservice.repository.jpa;

import com.querydsl.core.BooleanBuilder;
import com.querydsl.jpa.impl.JPAQueryFactory;
import hello.itemservice.domain.Item;
import hello.itemservice.domain.QItem;
import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.ItemSearchCond;
import hello.itemservice.repository.ItemUpdateDto;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.util.StringUtils;

import javax.persistence.EntityManager;
import java.util.List;
import java.util.Optional;

import static hello.itemservice.domain.QItem.*;

@Repository
@Transactional
public class JpaItemRepositoryV3 implements ItemRepository {

    private final EntityManager em;
    private final JPAQueryFactory query;

    public JpaItemRepositoryV3(EntityManager em) {
        this.em = em;
        this.query = new JPAQueryFactory(em);
    }

    @Override
    public Item save(Item item) {
        em.persist(item);
        return item;
    }

    @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        Item findItem = em.find(Item.class, itemId);
        findItem.setItemName(updateParam.getItemName());
        findItem.setPrice(updateParam.getPrice());
        findItem.setQuantity(updateParam.getQuantity());
    }

    @Override
    public Optional<Item> findById(Long id) {
        Item findItem = em.find(Item.class, id);
        return Optional.ofNullable(findItem);
    }

    @Override
    public List<Item> findAll(ItemSearchCond cond) {

        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        // 동적 쿼리
        // BooleanBuilder 를 통해 동적 쿼리를 구현할 수 있다.
        BooleanBuilder builder = new BooleanBuilder();

        // itemName 이 있는 경우
        if (StringUtils.hasText(itemName)) {
            builder.and(item.itemName.like("%" + itemName + "%"));
        }
        // maxPrice 가 있는 경우
        if (maxPrice != null) {
            // 부등호 표시가 불가능해서 문자로 대체한다.
            builder.and(item.price.loe(maxPrice));
        }

        return query
                .select(item)
                .from(item)
                // 동적 쿼리를 값으로 주면 해결된다.
                .where(builder)
                .fetch();
    }
}
```

<br>

### 📍 리팩토링

- BooleanBuilder 를 없애고 복잡한 로직을 method 로 만들어서 query 부분의 가독성을 매우 높혔다.
    - where 에서 null 이 입력될경우 해당 조건은 무시된다.
    - 비슷한 Query 가 생기면 method 를 재사용 할 수 있게된다.
    - 추후에 새로운 동적 쿼리 요구사항이 발생해도 간편하게 로직을 추가할 수 있다.

```java
    @Override
    public List<Item> findAll(ItemSearchCond cond) {

        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        return query
                .select(item)
                .from(item)
                // parameter 가 2개 이상일 경우 자동으로 and 가 작성됨
                .where(likeItemName(itemName), maxPrice(maxPrice))
                .fetch();
    }

    private BooleanExpression maxPrice(Integer maxPrice) {
        if (maxPrice != null) {
            return item.price.loe(maxPrice);
        }
            return null;
    }

    private BooleanExpression likeItemName(String itemName) {
        if (StringUtils.hasText(itemName)) {
            return item.itemName.like("%" + itemName + "%");
        }
        return null;
    }
```

## ✏️ Config

```java
package hello.itemservice.config;

import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.jpa.JpaItemRepositoryV3;
import hello.itemservice.repository.jpa.SpringDataJpaItemRepository;
import hello.itemservice.service.ItemService;
import hello.itemservice.service.ItemServiceV1;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;

@Configuration
@RequiredArgsConstructor
public class QuerydslConfig {

    private final EntityManager em;

    @Bean
    public ItemService itemService() {
        return new ItemServiceV1(itemRepository());
    }

    @Bean
    public ItemRepository itemRepository() {
        return new JpaItemRepositoryV3(em);
    }
}
```

<br>

## ✏️ import

import 까지 설정해주면 정상적으로 Test 가 작동된다.

```java
@Slf4j
@Import(QuerydslConfig.class)
@SpringBootApplication(scanBasePackages = "hello.itemservice.web")
public class ItemServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ItemServiceApplication.class, args);
	}
```
