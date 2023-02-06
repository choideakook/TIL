# Entity Class 개발
[🔗 JPA Entity 개념](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/4%20JPA/230203%201%20JPA%20개발.md)

### ❗️ cascade , fetch , 연관관계 편의 Method 반영이 안되어 있습니다.

[🔗 반영하는 방법](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/2%20도메인%20분석%20설계/230105%202%20Entity%20설계의%20주의점.md)

- Member

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter @Setter
public class Member {

    // PK 명을 @Column 으로 수정
    @Id @GeneratedValue
    @Column (name = "member_id")
    private Long id;

    private String name;

    // 임베디드가 된 필드
    @Embedded
    private Address address;

    // Order Class 의 member 필드에 의해시 의존관계의 거울이됨
    @OneToMany (mappedBy = "member") // non Owner 의 표시 (읽기만 가능)
    private List<Order> orders = new ArrayList<>();
}
```

- Address
	- [🔗 proctecte Class 로 만드는 법](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/3%20Application%20개발/230107%202%20주문%20도메인%20개발.md)

```java
package jpabook.jpashop.domain;

import lombok.Getter;

import javax.persistence.Embeddable;

@Embeddable
@Getter  // 값 Type 은 함부로 변경하면 안되기 때문에 Setter 는 만들지 않는다.
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // 생성자만 만들경우 JPA 스팩 때문에 에러가나서 형식적으로 기본생성자를 만들어준다.
    // 해당 Method 가 있을경우 해당 Class 는 함부로 new 로 생성하지 말라는 뜻
    protected Address(){
    }
		

    // 생성자에서 값을 모두 초기화해서 변경 불가능한 클래스로 만들어줘야한다.
    public Address(String city, String street, String zipcode) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    }
}
```

- Order

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

// Order 의 Table 명은 관례상 orders 로 해야한다.
@Entity
@Table (name = "orders")
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column (name = "order_id")
    private Long id;

    // Member 의 PK 에서 참조한 FK
    @ManyToOne   // N:1 의 연관관계
    @JoinColumn (name = "member_id")  // 연관관계의 주인이 표시 (조회, 저장, 수정, 삭제의 권한)
    private Member member;

    @OneToMany (mappedBy = "order")
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne
    @JoinColumn (name = "delivery_id")
    private Delivery delivery;

    // 주문시간
    private LocalDateTime orderDate;

    // 주문상태를 의미 [ order & cancel ] enum 으로 생성함
    @Enumerated (EnumType.STRING) // enum 값을 DB 에 저장하는 에노테이션
    private OrderStatus status;
    // EnumType.STRING : 이름으로 DB 에 저장 (항상 이것만 사용해야함)
    // EnumType.ORDINAL : 순서로 DB 에 저장 (중간에 순서가 바뀌면 망하기 때문에 사용금지) 기본값
}
```

- OrderStatus

```java
package jpabook.jpashop.domain;

public enum OrderStatus {
    ORDER, CANCEL
}
```

- Delivery

```java
package jpabook.jpashop.domain;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;

@Entity
@Getter
@Setter
public class Delivery {

    @Id
    @GeneratedValue
    @Column(name = "delivery_id")
    private Long id;

    @OneToOne (mappedBy = "delivery")
    private Order order;

    @Embedded
    private Address address;

    // enum [READY , COMP]
    @Enumerated (EnumType.STRING)
    private DeliveryStatus status;
}
```

- DeliveryStatus

```java
package jpabook.jpashop.domain;

public enum DeliveryStatus {
    READY , COMP
}
```

- Item  

[🔗 상속관계 매핑 전략](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/상속관계%20매핑%20전략.md)  
  
```java
package jpabook.jpashop.domain.item;

import jpabook.jpashop.domain.Category;
import jpabook.jpashop.exception.NotEnoughStockException;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

// 구현체를 만들예정이라 추상 Class 로 세팅
// 상속관계 매핑이기 때문에 상속관계 전략을 지정해야 함 (부모 Class 에 생성)
@Entity
@Inheritance (strategy = InheritanceType.SINGLE_TABLE) // 상속관계 매핑 전략
@DiscriminatorColumn (name = "dtype") //싱글테이블이라 저장할 때 구분을 하기위한 에노테이션
@Getter @Setter
public abstract class Item {

    @Id @GeneratedValue
    @Column (name = "item_id")
    private Long id;

    // 구현체가 바뀌어도 필요한 공통 속성
    private String name;
    private int price;
    private int stockQuantity;

    @ManyToMany (mappedBy = "items")  // 예제를 위한 N:N 관계
    private List<Category> categories = new ArrayList<>();
```

- OrderItem

```java
package jpabook.jpashop.domain;

import jpabook.jpashop.domain.item.Item;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;

@Entity
@Getter @Setter
public class OrderItem {

    @Id @GeneratedValue
    @Column (name = "order_item_id")
    private Long id;

    @ManyToOne
    @JoinColumn (name = "item_id")
    private Item item;

    @ManyToOne
    @JoinColumn (name = "order_id")
    private Order order;

    // 주문가격
    private int orderPrice;

    // 주문수량
    private int count;
}
```

- Book

```java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@Getter
@Setter
@DiscriminatorValue("B") // DB 에 저장될 때 구분하기위한 에노테이션 (괄호값을 안쓰면 Class 명으로 저장됨)
public class Book extends Item {

    private String author;
    private String isbn;
}
```

- Album

```java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@Getter @Setter
@DiscriminatorValue("A")
public class Album  extends Item{

    private String artist;
    private String etc;
}
```

- Movie

```java
package jpabook.jpashop.domain.item;

import lombok.Getter;
import lombok.Setter;

import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;

@Entity
@Getter
@Setter
@DiscriminatorValue("M")
public class Movie extends Item {

    private String director;
    private String actor;
}
```

## ✏️ 예제를 위한 N:N 관계

- Item
    - cartegory 와 연결하기위한 List 추가

```java
		@ManyToMany (mappedBy = "items")  // 예제를 위한 N:N 관계
    private List<Category> categories = new ArrayList<>();
```

- Category

```java
package jpabook.jpashop.domain;

import jpabook.jpashop.domain.item.Item;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter
@Setter
public class Category {

    @Id
    @GeneratedValue
    @Column(name = "category_id")
    private Long id;

    private String name;

    @ManyToMany
    @JoinTable(name = "category item", // 중간테이블에 연결
            joinColumns = @JoinColumn(name = "category_id"), // 중간 테이블에 맵핑
            inverseJoinColumns = @JoinColumn(name = "item_id") // 중간 테이블에서 item 쪽으로 맵핑
    )
    private List<Item> items = new ArrayList<>();
    // 객체는 상관없지만
    // DB 는 Collection 을 양쪽에서 가질 수 없기 때문에
    // 1:N , N:1 로 풀어낼 수 있는 중간 Tabel 이 있어야한다.

    // 자기 자신을 맵핑하는 방법 (셀프 양방향 연관관계 설정)
    @ManyToOne // 부모는 하나지만 자식은 많을 수 있으므로 N:1
    @JoinColumn(name = "parent_id")  // 부모
    private Category parent;

    @OneToMany(mappedBy = "parent") // 자식
    private List<Category> child = new ArrayList<>();
}
```

<br>

## ✏️ Table 생성확인

Entity Class 를 전부 생성해준후 컴파일 실행하고 DB 에 들어가면 자동으로 Table 이 생성된걸 볼 수 있다.

### 📍 참고

필드에서 Hibernate 에 의해 DB 로 등록될 때 필드명의 대문자를 소문자로 바꾸고 사이에 _ 를 추가해서 DB 에 등록해준다.

- ex)

```java
private LocalDateTime orderDate;

orderDate -DB 등록-> order_date
```
