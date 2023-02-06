# Spring data jpa 를 직접 의존하기

[🔗 원본 코드](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/5%20Spring%20Data%20Jpa/230204%202Spring%20Data%20JPA%20적용.md)

## ✏️ 리팩토링

OCP 와 DI 의 원칙을 지키기 위해 불필요했던 불필요한 절차를 없애고Spring data jpa 를 직접 의존하는 방식으로 리팩토링을 해보자

<br>

- Spring Data Jpa 의 기능은 최대한 살리면서 QueryDsl 도 편리하게 사용할 수 있는 구조
    - Service 는 두개의 Class 에 의존한다.
    - 기본적인 CRUD 기능은 Spring Data JPA 로
    - 복잡한 동적 쿼리는 QueryDsl 로

<img width="531" alt="s7811" src="https://user-images.githubusercontent.com/115536240/216855633-99a390cf-d588-42f1-bcd3-a1b0a97ce371.png">


<br>

## ✏️ Repository

### 📍 ItemRepositoryV2
Spring data JPA

- `JpaRepository` 를 상속하는 것으로 기본적인 Method 가 생성된다.

```java
package hello.itemservice.repository.v2;

import hello.itemservice.domain.Item;
import org.springframework.data.jpa.repository.JpaRepository;

// JpaRepository 가 @Repository 의 기능을 대신해준다.
public interface ItemRepositoryV2 extends JpaRepository<Item, Long> {
}
```

<br>

### 📍 ItemQueryReposiotryV2
QueryDSL

[🔗 QueryDSL 환경설정](https://github.com/choideakook/TIL/tree/main/Spring/7%20DB%20접근%20활용/6%20QueryDSL)

- Spring data JPA 의 약점인 동적 쿼리같은 복잡한 Qurery 를 담당한다.

```java
package hello.itemservice.repository.v2;

import com.querydsl.core.types.dsl.BooleanExpression;
import com.querydsl.jpa.impl.JPAQueryFactory;
import hello.itemservice.domain.Item;
import hello.itemservice.repository.ItemSearchCond;
import org.springframework.stereotype.Repository;
import org.springframework.util.StringUtils;

import javax.persistence.EntityManager;
import java.util.List;

import static hello.itemservice.domain.QItem.item;

@Repository
public class ItemQueryRepositoryV2 {

    // 의존관계 주입
    private final JPAQueryFactory query;

    public ItemQueryRepositoryV2(EntityManager em) {
        this.query = new JPAQueryFactory(em);
    }

    // findAll
    public List<Item> findAll(ItemSearchCond cond) {

        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        // 동적 쿼리 구현
        return query
                .select(item)
                .from(item)
                .where(likeItemName(itemName), maxPrice(maxPrice))
                .fetch();
    }

    // 복잡한 로직은 가독성을 위해 별도 Method 로 관리한다.
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

}
```

<br>

## ✏️ Service
ItemServiceV2

- `ItemRepositoryV2` 와 `ItemQueryRepositoryV2` 두 Class 를 의존해서 Serivce 로직을 만든다.

```java
package hello.itemservice.service;

import hello.itemservice.domain.Item;
import hello.itemservice.repository.ItemSearchCond;
import hello.itemservice.repository.ItemUpdateDto;
import hello.itemservice.repository.v2.ItemQueryRepositoryV2;
import hello.itemservice.repository.v2.ItemRepositoryV2;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.Optional;

@Service
@Transactional
@RequiredArgsConstructor
public class ItemServiceV2 implements ItemService {

    private final ItemRepositoryV2 itemRepositoryV2;
    private final ItemQueryRepositoryV2 itemQueryRepositoryV2;

    @Override
    public Item save(Item item) {
        return itemRepositoryV2.save(item);
    }

    @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        Item findItem = itemRepositoryV2.findById(itemId).orElseThrow();
        findItem.setItemName(updateParam.getItemName());
        findItem.setPrice(updateParam.getPrice());
        findItem.setQuantity(updateParam.getQuantity());
    }

    @Override
    public Optional<Item> findById(Long id) {
        return itemRepositoryV2.findById(id);
    }

    @Override
    public List<Item> findItems(ItemSearchCond itemSearch) {
        return itemQueryRepositoryV2.findAll(itemSearch);
    }
}
```

<br>

## ✏️ Config

- Service 에게 `ItemRepositoryV2` 와 `ItemQueryRepositoryV2` 를 주입해준다.
    - `ItemRepositoryV2` 는 `JpaRepository` 가 자동으로 Bean 등록을 해주기 때문에 수동으로  @Bean 등록을 해줄 필요가 없다.
- `ItemRepository` 는 test init 때문에 삭제하지 않고 두었다.

```java
package hello.itemservice.config;

import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.jpa.JpaItemRepositoryV3;
import hello.itemservice.repository.v2.ItemQueryRepositoryV2;
import hello.itemservice.repository.v2.ItemRepositoryV2;
import hello.itemservice.service.ItemService;
import hello.itemservice.service.ItemServiceV2;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;

@Configuration
@RequiredArgsConstructor
public class V2Config {

    private final EntityManager em;
    // ItemRepositoryV2 는 JpaRepository 가 자동으로 Bean 등록을 해준다.
    private final ItemRepositoryV2 itemRepositoryV2;

    @Bean
    public ItemService itemService() {
        return new ItemServiceV2(itemRepositoryV2, itemQueryRepositoryV2());
    }
    
    @Bean
    public ItemQueryRepositoryV2 itemQueryRepositoryV2() {
        return new ItemQueryRepositoryV2(em);
    }

    // test data init 때문에 살려둔 Bean
    @Bean
    public ItemRepository itemRepository() {
        return new JpaItemRepositoryV3(em);
    }
}
```

<br>

## ✏️ Import

- 정상적으로 작동한다.

```java
@Slf4j
@Import(V2Config.class)
@SpringBootApplication(scanBasePackages = "hello.itemservice.web")
public class ItemServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ItemServiceApplication.class, args);
	}
```

<br>

⚠️ Test 는 item repository 를 주입받아 작성되었기 때문에 사실 지금 코드와는 상관이 없다.

- OCP 원칙을 지키지 않았기 때문
- Application 이 잘 작동하면 코드에 문제가 없다는 뜻이다.
