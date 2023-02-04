# Transaction Test

## ✏️ Test 의  원칙

- Test 는 다른 Test 와 격리되어야 한다.
- Test 는 반복해서 실행할 수 있어야 한다.

Test 간의 영향을 주지 않기위해, 같은 Test 를 반복 실행해 검증하기 위해 Test method 가 종료되면 꼭 Rollback 을 해주어야 한다.

## ✏️ Test 에 Transaction 적용하기

[🔗 Transaction Manager](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20접근%20핵심%20원리/4%20Transaction%20활용/230130%202%20Transaction%20동기화.md)

```java
@SpringBootTest
class ItemRepositoryTest {

    @Autowired
    ItemRepository itemRepository;

    // Transaction Manager 와 status 선언
    @Autowired
    PlatformTransactionManager transactionManager;
    TransactionStatus status;

    // test 를 시작하기 전 Transaction 이 실행됨
    @BeforeEach
    void beforeEach() {
        // Transaction 시작
        status = transactionManager.getTransaction(new DefaultTransactionDefinition());
    }

    @AfterEach
    void afterEach() {
        //MemoryItemRepository 의 경우 제한적으로 사용
        if (itemRepository instanceof MemoryItemRepository) {
            ((MemoryItemRepository) itemRepository).clearStore();
        }
        // Transaction rollback
        transactionManager.rollback(status);
    }
```

⚠️ DataSource 와 Transaction Manager 는 Spring boot 가 자동으로 Bean 등록을 해준다.

- test 로 인해 등록된 data 가 method 가 종료된후 rollback 되어 Test 원칙을 준수할 수 있게된다.

```java
@SpringBootTest
class ItemRepositoryTest {

    @Autowired
    ItemRepository itemRepository;

    @Autowired
    PlatformTransactionManager transactionManager;
    TransactionStatus status;

    // test 를 시작하기 전 Transaction 이 실행됨
    @BeforeEach
    void beforeEach() {
        // Transaction 시작
        status = transactionManager.getTransaction(new DefaultTransactionDefinition());
    }

    @AfterEach
    void afterEach() {
        //MemoryItemRepository 의 경우 제한적으로 사용
        if (itemRepository instanceof MemoryItemRepository) {
            ((MemoryItemRepository) itemRepository).clearStore();
        }
        // Transaction rollback
        transactionManager.rollback(status);
    }
```

<br>