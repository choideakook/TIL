# Spring Data JPA 적용

## ✏️ 환경설정

### 📍 Dependencies

JPA 와 Spring data jpa 의 Dependency 는 공통이기 때문에 JPA 를 이미 등록했다면 추가로 Dependency 를 등록하지 않아도 된다.

[🔗 JPA Dependency 추가](https://github.com/choideakook/TIL/tree/main/Spring/7%20DB%20접근%20활용/4%20JPA)

<br>

## ✏️ Interface

- `JpaRepository< Eintity, PK type >` 를 상속하는 interface 를 생성한다.
    - 제네릭에 Entity 를 넣어주는 것 만으로도 기본적인 CRUD 기능이 Method 없이도 사용 가능해진다.
- 기본적인 CRUD 는 자동으로 제공되기 때문에 동적 Query 만 method 로 만들어주면 된다.
    - 동적 Query 는 find all , name 으로 조회, price 로 조회 그리고 name 과 price 로 조회 를 이용한다.
    - 하나의 Method 에 모든 Query 를 구현하지 않고 각각의 method 로 구현함
    - name 과 price 로 조회는 같은 기능을 두가지 방식으로 구현했다.
        - 방법 1. 은 코드는 적지만 가독성이 매우 떨어진다.
        - 방법 2. 는 코드는 더 길지만, 가독성이 더 좋아 선호되는 방법이다.

❗ Spring data JPA 는 동적 Query 에 매우 약하다.

동적 Query 는 Query DSL 로 구현하는것이 좋다.

```java
package hello.itemservice.repository.jpa;

import hello.itemservice.domain.Item;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

import java.util.List;

// 제네릭에 Entity 를 입력해 주는것 만으로도 기본적인 CRUD 는 Method 없이 사용할 수 있다.
public interface SpringDataJpaItemRepository extends JpaRepository<Item, Long> {

    // 상품 이름으로 Item 조회
    // like 를 사용해 parameter 가 포함된 ItemName 까지 조회
    List<Item> findByItemNameLike(String itemName);

    // 가격으로 Item 조회
    // Less Than 과 Equal 을 사용해 parameter 보다 작거나 같은 값을 조회
    List<Item> findByPriceLessThanEqual(Integer price);

    /**
     * 위의 두가지 Method 를 하나로 합친 method
     */

    // 방법 1. Query Method
    // 단순하게 필요한 내용을 줄줄 작성하면 된다.
    // 가독성이 매우 떨어지고 join 같은 기능을 사용하게되면 이름이 더 길어저
    // 실무에서 적용하는것은 매우 어렵다.
    List<Item> findByItemNameLikeAndPriceLessThanEqual(String itemName, Integer price);

    // 방법 2. Query 직접 실행
    // @Query 에 JPQL 문을 작성하면 된다.
    // Query 를 직접 작성할 경우 @Param 을 사용해야 한다.
    // @Param 은 spring data jpa 의 것을 사용해야 한다.
    @Query("select i from Item i where i.itemName like :itemName and i.price <= :price")
    List<Item> findItem(@Param("itemName") String itemName, @Param("price") Integer price);
}
```

<br>

## ✏️ Repository

SpringDataJpaItemRepository 는 interface 지만 ItemRepository 를 상속받은것이 아닌,
JpaRepository 를 상속받았기 때문에

지금 상황에서는 Service 계층에서 바로 DI 를 할 순없다.

- 별도로 ItemRepository 를 상속받은 Repository 를 생성후 Service 계층에 주입해주어야 COP 원칙에 위배되지 않는다.

```java
package hello.itemservice.repository.jpa;

import hello.itemservice.domain.Item;
import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.ItemSearchCond;
import hello.itemservice.repository.ItemUpdateDto;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.util.StringUtils;

import java.util.List;
import java.util.Optional;

@Repository
@Transactional
@RequiredArgsConstructor
public class JpaItemRepositoryV2 implements ItemRepository {
    
    // 의존관계 주입
    private final SpringDataJpaItemRepository repository;

    @Override
    public Item save(Item item) {
        return repository.save(item);
    }

    @Override
    public void update(Long itemId, ItemUpdateDto updateParam) {
        Item findItem = repository.findById(itemId).orElseThrow();
        findItem.setItemName(updateParam.getItemName());
        findItem.setPrice(updateParam.getPrice());
        findItem.setQuantity(updateParam.getQuantity());
    }

    @Override
    public Optional<Item> findById(Long id) {
        return repository.findById(id);
    }

    @Override
    public List<Item> findAll(ItemSearchCond cond) {
        String itemName = cond.getItemName();
        Integer maxPrice = cond.getMaxPrice();

        // 동적 쿼리
        if (StringUtils.hasText(itemName) && maxPrice != null) {
            return repository.findItems("%" + itemName + "%", maxPrice);
        } else if (StringUtils.hasText(itemName)) {
            return repository.findByItemNameLike("%" + itemName + "%");
        } else if (maxPrice != null) {
            return repository.findByPriceLessThanEqual(maxPrice);
        } else {
            return repository.findAll();
        }
    }
}
```

<br>

### 📍 분석

- Class 의존 관계

<img width="520" alt="s7521" src="https://user-images.githubusercontent.com/115536240/216801645-f4410d3d-5e1f-4391-98eb-f7964b40d2f6.png">

- 런타임 객체 의존 관계
    - 실질적으로 item Service 는 JpaItemRepositoryV2 가 주입된다.
    - JpaItemRepositoryV2 는 Spring data Jpa 가 만든 Proxy Repository 가 주입된다.

<img width="520" alt="s7522" src="https://user-images.githubusercontent.com/115536240/216801649-7bd2701e-2fbf-414f-95d9-b3c068642950.png">

<br>

## ✏️ Config

```java
package hello.itemservice.config;

import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.jpa.JpaItemRepositoryV2;
import hello.itemservice.repository.jpa.SpringDataJpaItemRepository;
import hello.itemservice.service.ItemService;
import hello.itemservice.service.ItemServiceV1;
import lombok.RequiredArgsConstructor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@RequiredArgsConstructor
public class SpringDataJpaConfig {

    private final SpringDataJpaItemRepository repository;

    @Bean
    public ItemService itemService() {
        return new ItemServiceV1(itemRepository());
    }

    @Bean
    public ItemRepository itemRepository() {
        return new JpaItemRepositoryV2(repository);
    }
}
```

<br>

## ✏️ Import

```java
@Slf4j
@Import(SpringDataJpaConfig.class)
@SpringBootApplication(scanBasePackages = "hello.itemservice.web")
public class ItemServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ItemServiceApplication.class, args);
	}
```

<br>

## ✏️ 실행

Test 를 실행해보니 코드에 아무런 문제가 없음에도 불구하고 에러가 발생한다.

```java
InvalidDataAccessApiUsageException: Parameter value [\] did not match expected type [java.lang.String (n/a)];
```

이 에러는 코드에 의해 발생한 에러가 아닌 Hibernate  5.6.6 ~5.6.7 버전 자체의 문제가 있어서 발생한 버그로,
현재 상황에서 개발자가 이 문제를 해결할 수 없다.

- like 관련 기능을 사용하면 해당 에러가 발생한다.

따라서 이런 경우에는 Hibernate 의 버전을 낮춰서 해결해야 한다.

- hibernate 의 버전은 External Libraries 에서 hibernate-core 로 검색해보면 확인할 수 있다.

### 📍 Hibernate 버전 다운

build.gradle 에서 버전을 설정할 수 있다.

```java
lugins {
	id 'org.springframework.boot' version '2.6.5'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

// Hibernate 버전 다운 그레이드
ext["hibernate.version"] = "5.6.5.Final"

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
```

버전을 바꾸니 문제가 해결되었다.
