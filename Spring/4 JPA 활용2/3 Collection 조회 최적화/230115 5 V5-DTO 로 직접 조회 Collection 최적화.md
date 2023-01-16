# V5-DTO 로 직접 조회 Collection 최적화

## ✏️ Collection 조회 최적화 06

V-4 에서 발생한 N+1 의 문제점을 해결할 수 있는 방법

1. V4 와 똑같이 일단 Order 와 ~ : 1 관계에 있는 data 를 불러옴
2. 1번에서 가져온 값에서 id 값만 골라서 변수로 저장함
3. order id 값 과 매칭되는 OrderItem 의 data 만 조회하는 JPQL 문을 작성함
    - in절로 order id 값만 필터링 해서 가져오면 된다.
4. 이대로 둘을 연결해줘도 되지만 조금 더 최적화를 위해 orderitem 을 map 형태로 변환시켜준다.
    - Order 의 id 를 key 값으로 만들어줌
5. 반복문을 통해 가져온 data 들을 Order id 를 기준으로 연결해준다.

<br>

- findAllByDto_optimization

```java
    public List<OrderQueryDto> findAllByDto_optimization() {

        // 1. Order data 를 모두 가져옴
        List<OrderQueryDto> result = findOrders();

        // 2. Order 의 Id 만 map 으로 골라냄
        List<Long> orderIds = result.stream()
                .map(o -> o.getOrderId())
                .collect(Collectors.toList());

        // 3. order ids 를 in 절에 넣어 id 를 기반으로한 orderItems 를 만들어냄
        List<OrderItemQueryDto> orderItems = em.createQuery(
                        "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name, oi.orderPrice, oi.count)" +
                                " from OrderItem oi" +
                                " join oi.item i" +
                                " where oi.order.id in :orderIds", OrderItemQueryDto.class)
                .setParameter("orderIds", orderIds)
                .getResultList();

        // 4. orderItems 를 그냥 사용해도 되지만 조금더 최적화를 하기위해 Map 으로 변경해줌
        // order id 를 key 값으로 Map 형태로 변경됨
        Map<Long, List<OrderItemQueryDto>> orderItemMap = orderItems.stream()
                .collect(Collectors.groupingBy(orderItemQueryDto -> orderItemQueryDto.getOrderId()));

        // 5. 반복문을 통해 result 값의 order id 를 key 값으로 order item 의 값들을 넣어줌
        result.forEach(o -> o.setOrderItems(orderItemMap.get(o.getOrderId())));

        return result;
    }
```

- findOrders

```java
    private List<OrderQueryDto> findOrders() {
        return em.createQuery(
                        "select new jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
                                " from Order o" +
                                " join o.member m" +
                                " join o.delivery d", OrderQueryDto.class)
                .getResultList();
    }
```

- controller 실행

```java
    // V5 JPA 에서 DTO 로 직접 조회 Collection 조회 최적화
    @GetMapping("/api/v5/orders")
    public List<OrderQueryDto> orderV5(){
        return orderQueryRepository.findAllByDto_optimization();
    }
```

<br>

### 🔍 출력물 확인

Order 의 data 를 조회하는 Query 한번,

Order Item 을 조히하는 Query 총 두번의 Query 문으로 해결되었다.

```java
select
        order0_.order_id as col_0_0_,
        member1_.name as col_1_0_,
        order0_.order_date as col_2_0_,
        order0_.status as col_3_0_,
        delivery2_.city as col_4_0_,
        delivery2_.street as col_4_1_,
        delivery2_.zipcode as col_4_2_ 
    from
        orders order0_ 
    inner join
        member member1_ 
            on order0_.member_id=member1_.member_id 
    inner join
        delivery delivery2_ 
            on order0_.delivery_id=delivery2_.delivery_id

// in 절로 order id 와 관련된 data 만 불러오는 query 문이 작성됨
select
        orderitem0_.order_id as col_0_0_,
        item1_.name as col_1_0_,
        orderitem0_.order_price as col_2_0_,
        orderitem0_.count as col_3_0_ 
    from
        order_item orderitem0_ 
    inner join
        item item1_ 
            on orderitem0_.item_id=item1_.item_id 
    where
        orderitem0_.order_id in (
            ? , ?
        )
```