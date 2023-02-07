# 주문 Repository , Service 개발

## ✏️ 주문 Reposiotry 개발

- 구현 서비스
    - 주문 저장 기능
    - 주문 특정 주문 조회 기능
    - [🔗 주문 검색 기능](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/3%20Application%20개발/230108%203%20주문%20검색%20기능.md)

```java
package jpabook.jpashop.repository;

import jpabook.jpashop.domain.Order;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import java.util.List;

@Repository
@RequiredArgsConstructor
public class OrderRepository {

    private final EntityManager em;

    public void save(Order order) {
        em.persist(order);
    }

    public Order findOne(Long id) {
        return em.find(Order.class, id);
    }
}
```

## ✏️ 주문 Service 개발

- 도메인 모델 패턴을 채용
    - 비즈니스 로직을 Entity Class 가 가지고 객체지향을 활용하는 모델이다.
    - 핵심 비즈니스 로직을 Entity Class 에 만들어 놓았기 때문에 Service 계층은 단순하게 Entity 에 필요한 요청을 위임하는 역할을 한다.
    - 반대로 Service 계층에 대부분의 비즈니스 로직을 처리하는것을 트렌젝션 스크립트 패턴 이라고 한다.
- 구현 서비스
    - 주문기능
    - 취소기능
    - [🔗 검색기능](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/3%20Application%20개발/230108%203%20주문%20검색%20기능.md)

### 📍 애노테이션 세팅과 DI

```java
package jpabook.jpashop.service;

import jpabook.jpashop.domain.Delivery;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.repository.ItemRepository;
import jpabook.jpashop.repository.MemberRepository;
import jpabook.jpashop.repository.OrderRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@RequiredArgsConstructor
@Transactional (readOnly = true)
public class OrderService {

    private final OrderRepository orderRepository;
    private final MemberRepository memberRepository;
    private final ItemRepository itemRepository;
```

<br>

### 📍 주문 기능

OderRepository 에 save 만 해줘도 cascade 옵션에 의해서 delivery 와 orderItem 의 정보들도 전부 persist 가 된다.

[🔗 CascadeType.All](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/2%20도메인%20분석%20설계/230105%202%20Entity%20설계의%20주의점.md)

```java
		/**
     * 주문 기능
     */
		// Prarmeter 값에는 주문을 하기위해 Web 에서 클라이언트에게 원하는 정보들을 넣어준다.
    @Transactional
    public Long order(Long memberId, Long itemId, int count) {

        // Entity 조회
        Member member = memberRepository.findOne(memberId);
        Item item = itemRepository.findOne(itemId);

        // 배송정보 생성
        Delivery delivery = new Delivery();
        delivery.setAddress(member.getAddress());

        // 주문상품 생성
        OrderItem orderItem = OrderItem.createOrderItem(item, item.getPrice(), count);

        // 주문 생성
        Order order = Order.createOrder(member, delivery, orderItem);

        // 주문 저장
        orderRepository.save(order);

        return order.getId();
    }
```

<br>

### 📍 주문 취소 기능
Transactional 의 하에서 Data 가 변경되었기 때문에,  
별도의 persist 나 update 없이 변경 감지에 의해 자동으로 data 가 update 된다.  
[🔗 변경 감지](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/4%20Web%20계층%20개발/230109%207%20변경%20감지와%20병합%20(Dirty%20Checking%20%26%20Merge).md)

```java
/**
* 주문 취소 기능
*/
@Transactional
public void cancelOrder(LongorderId) {

    // 주문 Entity 조회
    Order order = orderRepository.findOne(orderId);

    // 주문 취소
    order.cancel();
}
```
