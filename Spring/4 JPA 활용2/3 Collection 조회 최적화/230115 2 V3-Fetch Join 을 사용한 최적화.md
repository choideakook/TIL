# V3-Fetch Join 을 사용한 최적화

## ✏️ Collection 조회 최적화 03

### 📍 repository 에 fetch 를 사용한 JPQL method 를 생성

```sql
    public List<Order> findAllWithItem() {
        return em.createQuery(
                "select o from Order o" +
                        " join fetch o.member m" +
                        " join fetch o.delivery d" +
                        " join fetch o.orderItem oi" +
                        " join fetch oi.item i",Order.class)
                .getResultList();
    }
```

이렇게 생성할경우 1 : N 의 관계에 있는 order 와 orderItem 때문에 데이터량이 중복되어 뻥튀기 된다.

- order_id 값을 보면 하나의 주문에 주문내역이 2개씩 있는걸 확인할 수 있다.

<img width="500" alt="s4311" src="https://user-images.githubusercontent.com/115536240/212236912-827eebaa-166d-4763-84a7-0387a5c205b2.png">

- 이때 Order 를 기준으로 join 을 하게되면

<img width="500" alt="s4312" src="https://user-images.githubusercontent.com/115536240/212236919-f1b62230-8683-43e1-a841-651680f35f2b.png">

보다시피 order 에서 중복이 발생하게 된다.

- 이렇게 되면 JPA 에서 Data 를 가져올 때 두배로 가져오게 된다.
- V3 를 완성하고 실행해 봐도 같은결과가 나온다.

```java
    // fetch join 을 사용한 최적화
    @GetMapping("/api/v3/orders")
    public List<OrderDto> orderV3() {
        return orderRepository.findAllWithItem().stream()
                .map(o -> new OrderDto(o))
                .collect(Collectors.toList());
    }
```

🔍 출력물 확인

```java
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T11:16:29.53579",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "JPA1 BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    },
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T11:16:29.53579",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "JPA1 BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T11:16:29.559753",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "SPRING1 BOOK",
                "orderPrice": 20000,
                "count": 2
            },
            {
                "itemName": "SPRING2 BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T11:16:29.559753",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "SPRING1 BOOK",
                "orderPrice": 20000,
                "count": 2
            },
            {
                "itemName": "SPRING2 BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    }
]
```

<br>

### 📍 distinct 로 문제 해결

- JPQL 에 distinct 를 포함하면 SQL 문에도 distinct 가 포함된다.
- SQL 에서 distinct 는 완전히 중복되는 구절을 하나로 합처준다.
    - 이 경우에서는 order 를 제외하면 중복이 없기때문에 실질적으로 가져오는 Data 는 이 전과 동일하다.
- distinct 가 있을경우 가져온 DB 에서 중복을 한번 더 검토해준다.
    - 결과적으로 우리가 보는 화면에서는 중복된 order 가 하나로 합처진걸 볼 수 있다.

```java
public List<Order> findAllWithItem() {
        return em.createQuery(
                // select 다음으로 distinct 를 넣어줌
                "select distinct o from Order o" +
                        " join fetch o.member m" +
                        " join fetch o.delivery d" +
                        " join fetch o.orderItems oi" +
                        " join fetch oi.item i",Order.class)
                .getResultList();
    }
```

🔍 출력물 확인

- order 의 중복이 제거되었다.

```java
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T11:38:59.982803",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "JPA1 BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T11:39:00.008893",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "orderStatus": "ORDER",
        "orderItems": [
            {
                "itemName": "SPRING1 BOOK",
                "orderPrice": 20000,
                "count": 2
            },
            {
                "itemName": "SPRING2 BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    }
]
```

<br>

### ❗️ Collection fetch join 의 문제점

1. distinct 를 사용할 경우 페이징 정상작동이 안되는 치명적인 단점을 가지고있다.
    - 결과 적으로 사용자 에게 보여지는 data 에서는 order 의 중복이 제거되지만 실질적으로 Entity 에서 가져오는 data 는 중복이 제거되지 않은 data 이다.
    - 이 경우 페이징을 하게 될 경우 중복이 제거 되지 않은 상태에서 페이징이 되고 이후에 중복을 제거하게되면 처리가 곤란해 지기 때문에 페이징 기능을 재공하지 못하게 된다.
    - 결국 모든 정보를 저장해 hibernate 가 가져운 정보들을 Memory 에 서 페이징을 하게된다.
        - 페이징 기능 자체가 Data 가 너무 커 한번에 조회하기 어려워 나눠서 조회하는 기능인데 모든 Data 를 Memory 에 저장하게 될 경우 paging 이 의미없게 되고 잘못하면 과부화에 걸리게 될수도 있다.
2. Collection fetch join 은 한번만 사용할 수 있다.
    - Collection 이 둘 이상일 경우 뻥튀기된 데이터에 한번 더 뻥튀기가 되는 상황이므로 어느것을 기준으로 정리해야 되는지 판단이 어려워저 data 가 부정합하게 조회될 위험이 있다.
