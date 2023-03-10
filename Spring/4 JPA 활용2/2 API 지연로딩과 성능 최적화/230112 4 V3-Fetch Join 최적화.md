# V3-Fetch Join ์ต์ ํ

[๐ย fetch join ์ฌ์ฉ๋ฐฉ๋ฒ](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Fetch%20Join.md)

## โ๏ธย ย ์ง์ฐ ๋ก๋ฉ๊ณผ ์กฐํ ์ฑ๋ฅ ์ต์ ํ 03

- Entity ๋ฅผ ์กฐํํด DTO ๋ก ๋ณํํํ ์กฐํํ๋ ๋ฐฉ๋ฒ
- fetch join ์ ์ฌ์ฉํ ์กฐํ๋ฐฉ๋ฒ
- ์์์ฑ Context ๊ฐ ํ๋๊ฐ์ ์ฑ์ฐ๊ธฐ ์ํด ์ฟผ๋ฆฌ๋ฌธ์ ์ง์  ๋๋ฆฌ๊ธฐ์ ์ repository ์ fetch join ์ ์ฌ์ฉํด ํ์ํ data ๋ฅผ ๋ฏธ๋ฆฌ ์ค๋นํด๋๋ ๋ฐฉ๋ฒ
    - ์ด๋ฏธ ์กฐํ๊ฐ ์๋ฃ๋ ์ํ ์ด๋ฏ๋ก ์ง์ฐ๋ก๋ฉ์ด ๋ฐ์ํ์ง ์๋๋ค.

<br>

### ๐ย Repository ์ createQuery Method ๋ฅผ ์์ฑํด์ค๋ค

โ ๏ธย fetch ๋ SQL ์๋ ์๋ JPA ์ ๊ธฐ์ ์ ์ธ ๋ช๋ น์ด์ด๋ค.

```java
// ๋ฏธ๋ฆฌ ํ์ํ ๋ฐ์ดํฐ์ ์ฟผ๋ฆฌ๋ฌธ์ ํ๋ฒ์ ๋ณด๋ด๋๋ method
    public List<Order> findAllwithMemberDelivery() {
        return em.createQuery(
                "select o from Order o" +
                        "join fetch o.member m" +
                        "join fetch o.delivery d", Order.class
        ).getResultList();
    } 
```

<br>

### ๐ย Controller V3 method

๋ฏธ๋ฆฌ ๊ฐ์ ธ์จ DB ๋ก stream ์ ๋๋ ค ์ํ๋ ์ ๋ณด๋ง DTO ์์ ๊ฐ์ ธ์ค๋ฉด ๋๋ค.

```java
@GetMapping("/api/v3/simple-orders")
    public List<SimpleOrderDto> ordersV3() {
        // ์์์ฑ context ๊ฐ ์ฟผ๋ฆฌ๋ฅผ ๋ณด๋ด๊ธฐ ์ ์ ๋ฏธ๋ฆฌ ์ฟผ๋ฆฌ๋ฅผ ๋ณด๋ด Data ๋ฅผ ์ ๋ถ ๊ฐ์ ธ์ด
        List<Order> orders = orderRepository.findAllWithMemberDelivery();
        return orders.stream()
                .map(o -> new SimpleOrderDto(o))
                .collect(Collectors.toList());
    }
```

<br>

### ๐ย ์ฟผ๋ฆฌ๋ฌธ ํ์ธ

ํ๋ฒ์ ์ฟผ๋ฆฌ๋ก ํ์ํ DB ์ ์ ๋ณด๋ค์ ๊ฐ์ ธ์ ์ธํํ๋ค.

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