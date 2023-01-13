# V3-Fetch Join 최적화

[🔗 fetch join 사용방법](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Fetch%20Join.md)

## ✏️  지연 로딩과 조회 성능 최적화 03

- Entity 를 조회해 DTO 로 변환한후 조회하는 방법
- fetch join 을 사용한 조회방법
- 영속성 Context 가 필드값을 채우기 위해 쿼리문을 직접 돌리기전에 repository 에 fetch join 을 사용해 필요한 data 를 미리 준비해놓는 방법
    - 이미 조회가 완료된 상태 이므로 지연로딩이 발생하지 않는다.

<br>

### 📍 Repository 에 createQuery Method 를 생성해준다

⚠️ fetch 는 SQL 에는 없는 JPA 의 기술적인 명령어이다.

```java
// 미리 필요한 데이터의 쿼리문을 한번에 보내두는 method
    public List<Order> findAllwithMemberDelivery() {
        return em.createQuery(
                "select o from Order o" +
                        "join fetch o.member m" +
                        "join fetch o.delivery d", Order.class
        ).getResultList();
    } 
```

<br>

### 📍 Controller V3 method

미리 가져온 DB 로 stream 을 돌려 원하는 정보만 DTO 에서 가져오면 된다.

```java
@GetMapping("/api/v3/simple-orders")
    public List<SimpleOrderDto> ordersV3() {
        // 영속성 context 가 쿼리를 보내기 전에 미리 쿼리를 보내 Data 를 전부 가져옴
        List<Order> orders = orderRepository.findAllWithMemberDelivery();
        return orders.stream()
                .map(o -> new SimpleOrderDto(o))
                .collect(Collectors.toList());
    }
```

<br>

### 🔍 쿼리문 확인

한번의 쿼리로 필요한 DB 의 정보들을 가져와 세팅한다.

```java
select
        order0_.order_id as order_id1_6_0_,
        member1_.member_id as member_i1_4_1_,
        delivery2_.delivery_id as delivery1_2_2_,
        order0_.delivery_id as delivery4_6_0_,
        order0_.member_id as member_i5_6_0_,
        order0_.order_date as order_da2_6_0_,
        order0_.status as status3_6_0_,
        member1_.city as city2_4_1_,
        member1_.street as street3_4_1_,
        member1_.zipcode as zipcode4_4_1_,
        member1_.name as name5_4_1_,
        delivery2_.city as city2_2_2_,
        delivery2_.street as street3_2_2_,
        delivery2_.zipcode as zipcode4_2_2_,
        delivery2_.status as status5_2_2_ 
    from
        orders order0_ 
    inner join
        member member1_ 
            on order0_.member_id=member1_.member_id 
    inner join
        delivery delivery2_ 
            on order0_.delivery_id=delivery2_.delivery_id
```