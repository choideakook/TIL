# V2-Entity 를 DTO 로 변환

## ✏️  지연 로딩과 조회 성능 최적화 02

Entity 를 조회한 후 별도의 DTO 를 생성해 필요한 값만 골라서 출력하는 방식

```java
    @GetMapping("/api/v2/simple-orders")
    public List<SimpleOrderDto> ordersV2 () {
        return orderRepository.findAllByCriteria(new OrderSearch()).stream()
                // DB 에서 찾아온 orders 를 DTO 에 주입해 stream 을 돌림
                .map(o -> new SimpleOrderDto(o))
                .collect(Collectors.toList());
    }

    // 조회를 원하는 필드 (API 스팩) 를 정확하게 입력해줘야 한다.
    @Data
    static class SimpleOrderDto{
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private OrderStatus orderStatus;
        private Address address;

        // 필드값을 주입하기 위한 생성자 생성
        public SimpleOrderDto(Order order) {
            orderId = order.getId();
            name = order.getMember().getName();
            orderDate = order.getOrderDate();
            orderStatus = order.getStatus();
            address = order.getDelivery().getAddress();
        }
    }
```

### 🔍 출력물 확인

- 원하는 정보만 출력이된다.
- 참고로 address 는 Entity 가 아닌 value Object 이다.

```java
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-12T12:09:12.975786",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        }
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-12T12:09:12.999754",
        "orderStatus": "ORDER",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        }
    }
]
```

<br>

### 📍 V1 과 V2 의 공통적인 문제점

LAZY 로딩으로 인해 DB 쿼리가 너무 많이 호출되어버림

- Member 와 Delivery 를 주입하는 순간 LAZY 가 초기화 되기 때문
- 주문 리스트를 조회하기위해 3개의 Table 을 조회 해야함
    - Order, Member, Delivery

```java
public SimpleOrderDto(Order order) {
            orderId = order.getId();
            name = order.getMember().getName(); // LAZY 초기화
            orderDate = order.getOrderDate();
            orderStatus = order.getStatus();
            address = order.getDelivery().getAddress(); // LAZY 초기화
        }
```

⚠️ LAZY 초기화란

- 영속성 context 가 order 의 id 값으로 주입해줄 필드값을 찾아줌
- 만약 필드값이 없는경우 (다른 Entity 가 필드인 경우) DB 에 쿼리문로 필드값을 찾아오게된다.
- Order 는 findOrders 를 통해 한번의 쿼리로 모든 정보를 가져올 수 있다.
- Order 와 연관관계에 있는 Member 와 Delivery 는 별도의 쿼리문으로 값을 받아올 수 있는데, 한번에 하나의 정보만 호출하기 때문에 Stream 을 돌때마다 계속해서 쿼리문을 사용해야 한다. (LAZY 초기화)
- 결국 Order 의 길이에 따라 쿼리문이 1 + N 으로 늘어나게된다. (N + 1 문제)

### 🔍 쿼리 출력문

총 5번 DB 에 쿼리문을 보냈다.

```java
// order 의 DB 2개 전부 조회
    select
        order0_.order_id as order_id1_6_,
        order0_.delivery_id as delivery4_6_,
        order0_.member_id as member_i5_6_,
        order0_.order_date as order_da2_6_,
        order0_.status as status3_6_ 
    from
        orders order0_ 
    inner join
        member member1_ 
            on order0_.member_id=member1_.member_id 
    where
        1=1 limit ?

// order 첫번째의 member db
select
        member0_.member_id as member_i1_4_0_,
        member0_.city as city2_4_0_,
        member0_.street as street3_4_0_,
        member0_.zipcode as zipcode4_4_0_,
        member0_.name as name5_4_0_ 
    from
        member member0_ 
    where
        member0_.member_id=?

//order 첫번째의 delivery db
select
        delivery0_.delivery_id as delivery1_2_0_,
        delivery0_.city as city2_2_0_,
        delivery0_.street as street3_2_0_,
        delivery0_.zipcode as zipcode4_2_0_,
        delivery0_.status as status5_2_0_ 
    from
        delivery delivery0_ 
    where
        delivery0_.delivery_id=?

// order 두번째의 member db
select
        member0_.member_id as member_i1_4_0_,
        member0_.city as city2_4_0_,
        member0_.street as street3_4_0_,
        member0_.zipcode as zipcode4_4_0_,
        member0_.name as name5_4_0_ 
    from
        member member0_ 
    where
        member0_.member_id=?

// order 두번째의 delivery db
select
        delivery0_.delivery_id as delivery1_2_0_,
        delivery0_.city as city2_2_0_,
        delivery0_.street as street3_2_0_,
        delivery0_.zipcode as zipcode4_2_0_,
        delivery0_.status as status5_2_0_ 
    from
        delivery delivery0_ 
    where
        delivery0_.delivery_id=?
```
