# V6-DTO로 직접조회 Flat data 최적화

## ✏️ Collection 조회 최적화 07

- OrderFlatDto
    
    data 를 한번에 조회하기 위해 모든 필드를 생성했다.
    

```java
package jpabook.jpashop.repository.order.query;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;

import java.time.LocalDateTime;
import java.util.List;

@Data
public class OrderFlatDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;

    private String itemName;
    private int orderPrice;
    private int count;

    public OrderFlatDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address, String itemName, int orderPrice, int count) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
        this.itemName = itemName;
        this.orderPrice = orderPrice;
        this.count = count;
    }
}
```

- Query Repository
    
    DTO 생성자의 값을을 전부 넣고 전부 join 으로 연결
    

```java
    public List<OrderFlatDto> findAllByDto_flat() {
        return em.createQuery(
                "select new jpabook.jpashop.repository.order.query.OrderFlatDto(o.id, m.name, o.orderDate, o.status, d.address, i.name, oi.orderPrice, oi.count)" +
                        " from Order o" +
                        " join o.member m" +
                        " join o.delivery d" +
                        " join o.orderItems oi" +
                        " join oi.item i", OrderFlatDto.class)
                .getResultList();
    }
```

- Controller

```java
    // V6 JPA 에서 DTO 로 직접 조회 Flat Data 최적화
    @GetMapping("/api/v6/orders")
    public List<OrderFlatDto> orderV6(){
        return orderQueryRepository.findAllByDto_flat();
    }
```

<br>

### 🔍 출력물 확인

한번의 쿼리로 모든 data 를 조회했지만 item 값이 하나의 order 에 포함되지 않고 각각 나눠처 order 가 중복되 총 4개의 결과물이 출력되었다.

```java
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T19:57:26.894988",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "itemName": "JPA1 BOOK",
        "orderPrice": 10000,
        "count": 1
    },
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T19:57:26.894988",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "itemName": "JPA2 BOOK",
        "orderPrice": 20000,
        "count": 2
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T19:57:26.919257",
        "orderStatus": "ORDER",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "itemName": "SPRING1 BOOK",
        "orderPrice": 20000,
        "count": 2
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T19:57:26.919257",
        "orderStatus": "ORDER",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "itemName": "SPRING2 BOOK",
        "orderPrice": 40000,
        "count": 4
    }
]
```

거기다 스팩도 Order Query Dto 가아닌 Flast Dtro 로 바뀌어버렸다.

<br>

## ✏️ 문제점 해결하기

- OrderQueryDto
    - @EqualsAndHashCode(of = "orderId") 어노테이션 추가
        - orderId 의 중복을 묶어줌
    - orderItems 를 포함한 생성자 추가

```java
package jpabook.jpashop.repository.order.query;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;
import lombok.EqualsAndHashCode;

import java.time.LocalDateTime;
import java.util.List;

@Data
@EqualsAndHashCode(of = "orderId")
public class OrderQueryDto {

    private Long orderId;
    private String name;
    private LocalDateTime orderDate;
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemQueryDto> orderItems;

    public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
    }
    // orderItems 를 추가한 생성자 추가
    public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address, List<OrderItemQueryDto> orderItems) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
        this.orderItems = orderItems;
    }
}
```

- OrderFlatDto 의 return  스팩을 OrderQueryDto 로 바꾸는 로직 추가

```java
    // V6 JPA 에서 DTO 로 직접 조회 Flat Data 최적화
    @GetMapping("/api/v6/orders")
    public List<OrderQueryDto> orderV6(){
        List<OrderFlatDto> flats = orderQueryRepository.findAllByDto_flat();

        // OrderQueryDto 로 출력값의 스팩 변경
        // flats 를 수동으로 분해하고 재구성해서 스팩을 변경할 수 있다.
        return flats.stream()
                .collect(groupingBy(o -> new OrderQueryDto(o.getOrderId(),o.getName(), o.getOrderDate(), o.getOrderStatus(), o.getAddress()),
                        mapping(o -> new OrderItemQueryDto(o.getOrderId(), o.getItemName(), o.getOrderPrice(), o.getCount()), toList())
                )).entrySet().stream()
                .map(e -> new OrderQueryDto(e.getKey().getOrderId(), e.getKey().getName(), e.getKey().getOrderDate(), e.getKey().getOrderStatus(), e.getKey().getAddress(), e.getValue()))
                .collect(toList());
    }
```

🔍 출력물 확인

쿼리문도 한번만 실행되었다.

```java
[
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2023-01-13T20:27:42.894261",
        "orderStatus": "ORDER",
        "address": {
            "city": "전주",
            "street": "2",
            "zipcode": "2222"
        },
        "orderItems": [
            {
                "orderId": 11,
                "itemName": "SPRING1 BOOK",
                "orderPrice": 20000,
                "count": 2
            },
            {
                "orderId": 11,
                "itemName": "SPRING2 BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    },
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-13T20:27:42.871158",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipcode": "1111"
        },
        "orderItems": [
            {
                "orderId": 4,
                "itemName": "JPA1 BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "orderId": 4,
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    }
]
```

<br>

## ✏️ 정리

- 1번의 Query 로 모든 data 를 조회할 수 있다.
- join 으로 인해 DB 에서 application 으로 전달되는 데이터가 중복이 추가된상태기 때문에 상황에 따라서 V5 보다 느릴 수 있다.
    - data 가 매우 많지 않을경우 대부분 V6 의 처리속도가 더 빠르다.
- application 에서 스팩 변경으로 인한 추가작업의 부담이 생긴다.
- 페이징이 불가능하다.
    - 이미 db 에서 중복이 된상태로 출력이 되기때문에 Order 를 기준으로 페이징이 불가능함