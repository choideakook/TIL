# V1,2-Entity 직접노출 & DTO 로 변환

## ✏️ Collection 조회 최적화 01 -  Entity 직접 노출

- Entity 를 직접 노출했기 때문에 Entity 가 변경되면 API 스팩도 변경된다.
    - [🔗 Entity 가 변경됬을때 문제점](https://github.com/choideakook/TIL/blob/main/Spring/4%20JPA%20활용2/1%20API%20개발%20기본/230111%201%20회원%20등록%20API.md)
- 트렌젝션 안에서 지연로딩이 필요하다.
- 양방향 연관관계에 문제가 생긴다.

### 📍 V1 로직

1 : 1 / N : 1 에서도 발생했던 문제가 그대로 발생한다.

[🔗 ~ : 1 **V1-**Entity 를 직접 노출](https://github.com/choideakook/TIL/blob/main/Spring/4%20JPA%20활용2/2%20API%20지연로딩과%20성능%20최적화/230112%202%20V1-Entity%20를%20직접%20노출.md)

```java
package jpabook.jpashop.api;

import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.repository.OrderRepository;
import jpabook.jpashop.repository.OrderSearch;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@RequiredArgsConstructor
public class OrderApiController {

    private final OrderRepository orderRepository;

    @GetMapping("/api/v1/orders")
    public List<Order> ordersV1 (){
        List<Order> all = orderRepository.findAllByCriteria(new OrderSearch());
        for (Order order : all) {
            order.getMember().getName(); //Lazy 강제 초기화
            order.getDelivery().getAddress(); //Lazy 강제 초기환
            List<OrderItem> orderItems = order.getOrderItems();
            orderItems.stream().forEach(o -> o.getItem().getName()); //Lazy 강제초기화
        }
        return all;
    }
}
```

사용하기도 까다롭고 문제점도 많아서 실무에서 사용하기는 어려운 방법이다.

<br>

## ✏️ Collection 조회 최적화 02 - Entity 를 DTO 로 변환

❗️Order 만 DTO 로 변환 하는것이 아닌 그 안의 OrderItem 도 DTO 로 변환해주어야 한다.

```java
package jpabook.jpashop.api;

import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.domain.OrderStatus;
import jpabook.jpashop.repository.OrderRepository;
import jpabook.jpashop.repository.OrderSearch;
import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;
import java.util.List;
import java.util.stream.Collectors;

@RestController
@RequiredArgsConstructor
public class OrderApiController {

    private final OrderRepository orderRepository;

    // v2 엔티티를 DTO 로 변환하는 방식
    @GetMapping("/api/v2/orders")
    public List<OrderDto> orderV2() {
        return orderRepository.findAllByCriteria(new OrderSearch()).stream()
                .map(o -> new OrderDto(o))
                .collect(Collectors.toList());
    }

    @Data
    static class OrderDto {
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private Address address;
        private OrderStatus orderStatus;
        private List<OrderItemDto> orderItems;

        public OrderDto(Order order) {
            orderId = order.getId();
            name = order.getMember().getName();
            orderDate = order.getOrderDate();
            orderStatus = order.getStatus();
            address = order.getDelivery().getAddress();
						// orderItems 도 Entity 로 노출되면 안되서 DTO 를 만들어 줘야 한다.
            orderItems = order.getOrderItems().stream()
                    .map(orderItem -> new OrderItemDto(orderItem))
                    .collect(Collectors.toList());
        }
    }

		// orderItems 의 Entity 를 DTO 로 변환
    @Data
    static class OrderItemDto{
        private String itemName;//상품 명
        private int orderPrice; //주문 가격
        private int count; //주문 수량

        public OrderItemDto(OrderItem orderItem) {
            itemName = orderItem.getItem().getName();
            orderPrice = orderItem.getOrderPrice();
            count = orderItem.getCount();
        }
    }
}
```

<br>

### 🔍 출력물 확인

```java
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2023-01-12T22:44:41.1529",
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
        "orderDate": "2023-01-12T22:44:41.176127",
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

### 📍 문제점

Collection 을 사용하니 지연 로딩으로 인한 쿼리문이 ~ : 1 때 보다 훨신 더 많이 발생되어 버렸다.