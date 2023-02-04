# @Transactional

어노테이션을 사용하면 Transaction Manager 와 Status 의 기능을 대신할 수 있다.

```java
package hello.itemservice.domain;

import hello.itemservice.repository.ItemRepository;
import hello.itemservice.repository.ItemSearchCond;
import hello.itemservice.repository.ItemUpdateDto;
import hello.itemservice.repository.memory.MemoryItemRepository;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
@Transactional // 어노테이션 추가
class ItemRepositoryTest {

    @Autowired
    ItemRepository itemRepository;

    // 복잡한 Transaction 관련 로직을 어노테이션이 대신해준다.
    
   // 객체 지향을 위해 남겨놓은 After each
   // 지금상황에서는 아무 기능도 하지 않는다.
    @AfterEach
    void afterEach() {
        if (itemRepository instanceof MemoryItemRepository) {
            ((MemoryItemRepository) itemRepository).clearStore();
        }
    }
```

<br>

## ✏️ @Transactional 의 원리

@Transactional 은 로직이 성공적으로 수행되면 Commit 하도록 동작된다.

[🔗  @Transactional](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20접근%20핵심%20원리/4%20Transaction%20활용/230130%206%20Transaction%20AOP%20정리.md)

하지만 Test 에서 @Transactional 을 사용할 경우
Transaction 안에서 Test 를 실행하고 종료되면 무조건 rollback 으로 동작된다.

![s7211.png](@Transactional%2045b2565cca4940fba10503058653ecc2/s7211.png)

@Transactional 의 원리는 동일하지만 Test 에서는 마지막에 Rollback 하는 부분이 차이점이다.

⚠️ Test 의 @Transactional 이 작동되고 @Transactional 이 있는 Service method 가 호출되면 Serivce 에 있는 트렌젝션은 동작되고 있는 test 의 트랜젝션과 합처져 같은 @Transactional 이 사용되고 그 뜻은 같은 Connection 을 사용한다는 말과 같다.