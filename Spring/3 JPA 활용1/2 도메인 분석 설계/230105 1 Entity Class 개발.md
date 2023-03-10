# Entity Class κ°λ°
[π JPA Entity κ°λ](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20μ κ·Ό%20νμ©/4%20JPA/230203%201%20JPA%20κ°λ°.md)

### βοΈ cascade , fetch , μ°κ΄κ΄κ³ νΈμ Method λ°μμ΄ μλμ΄ μμ΅λλ€.

[πΒ λ°μνλ λ°©λ²](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20νμ©1/2%20λλ©μΈ%20λΆμ%20μ€κ³/230105%202%20Entity%20μ€κ³μ%20μ£Όμμ .md)

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

    // PK λͺμ @Column μΌλ‘ μμ 
    @Id @GeneratedValue
    @Column (name = "member_id")
    private Long id;

    private String name;

    // μλ² λλκ° λ νλ
    @Embedded
    private Address address;

    // Order Class μ member νλμ μν΄μ μμ‘΄κ΄κ³μ κ±°μΈμ΄λ¨
    @OneToMany (mappedBy = "member") // non Owner μ νμ (μ½κΈ°λ§ κ°λ₯)
    private List<Order> orders = new ArrayList<>();
}
```

- Address
	- [π proctecte Class λ‘ λ§λλ λ²](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20νμ©1/3%20Application%20κ°λ°/230107%202%20μ£Όλ¬Έ%20λλ©μΈ%20κ°λ°.md)

```java
package jpabook.jpashop.domain;

import lombok.Getter;

import javax.persistence.Embeddable;

@Embeddable
@Getter  // κ° Type μ ν¨λΆλ‘ λ³κ²½νλ©΄ μλκΈ° λλ¬Έμ Setter λ λ§λ€μ§ μλλ€.
public class Address {

    private String city;
    private String street;
    private String zipcode;

    // μμ±μλ§ λ§λ€κ²½μ° JPA μ€ν© λλ¬Έμ μλ¬κ°λμ νμμ μΌλ‘ κΈ°λ³Έμμ±μλ₯Ό λ§λ€μ΄μ€λ€.
    // ν΄λΉ Method κ° μμκ²½μ° ν΄λΉ Class λ ν¨λΆλ‘ new λ‘ μμ±νμ§ λ§λΌλ λ»
    protected Address(){
    }
		

    // μμ±μμμ κ°μ λͺ¨λ μ΄κΈ°νν΄μ λ³κ²½ λΆκ°λ₯ν ν΄λμ€λ‘ λ§λ€μ΄μ€μΌνλ€.
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

// Order μ Table λͺμ κ΄λ‘μ orders λ‘ ν΄μΌνλ€.
@Entity
@Table (name = "orders")
@Getter @Setter
public class Order {

    @Id @GeneratedValue
    @Column (name = "order_id")
    private Long id;

    // Member μ PK μμ μ°Έμ‘°ν FK
    @ManyToOne(fetch = LAZY)   // N:1 μ μ°κ΄κ΄κ³
    @JoinColumn (name = "member_id")  // μ°κ΄κ΄κ³μ μ£ΌμΈμ΄ νμ (μ‘°ν, μ μ₯, μμ , μ­μ μ κΆν)
    private Member member;

    @OneToMany (mappedBy = "order", cascade = ALL)
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne(fetch = LAZY, cascade = ALL)
    @JoinColumn (name = "delivery_id")
    private Delivery delivery;

    // μ£Όλ¬Έμκ°
    private LocalDateTime orderDate;

    // μ£Όλ¬Έμνλ₯Ό μλ―Έ [ order & cancel ] enum μΌλ‘ μμ±ν¨
    @Enumerated (EnumType.STRING) // enum κ°μ DB μ μ μ₯νλ μλΈνμ΄μ
    private OrderStatus status;
    // EnumType.STRING : μ΄λ¦μΌλ‘ DB μ μ μ₯ (ν­μ μ΄κ²λ§ μ¬μ©ν΄μΌν¨)
    // EnumType.ORDINAL : μμλ‘ DB μ μ μ₯ (μ€κ°μ μμκ° λ°λλ©΄ λ§νκΈ° λλ¬Έμ μ¬μ©κΈμ§) κΈ°λ³Έκ°
    
    //== μ°κ΄κ΄κ³ νΈμ Method ==//
    
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }

    public void setOrderItem(OrderItem orderItem) {
        this.orderItems.add(orderItem);
        orderItem.setOrder(this);
    }

    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    }
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

    @OneToOne (mappedBy = "delivery", fetch = LAZY)
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

    @ManyToOne(fetch = LAZY)
    @JoinColumn (name = "item_id")
    private Item item;

    @ManyToOne(fetch = LAZY)
    @JoinColumn (name = "order_id")
    private Order order;

    // μ£Όλ¬Έκ°κ²©
    private int orderPrice;

    // μ£Όλ¬Έμλ
    private int count;
}
```
  
- Item  

[π μμκ΄κ³ λ§€ν μ λ΅](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/μμκ΄κ³%20λ§€ν%20μ λ΅.md)  
  
```java
package jpabook.jpashop.domain.item;

import jpabook.jpashop.domain.Category;
import jpabook.jpashop.exception.NotEnoughStockException;
import lombok.Getter;
import lombok.Setter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

// κ΅¬νμ²΄λ₯Ό λ§λ€μμ μ΄λΌ μΆμ Class λ‘ μΈν
// μμκ΄κ³ λ§€νμ΄κΈ° λλ¬Έμ μμκ΄κ³ μ λ΅μ μ§μ ν΄μΌ ν¨ (λΆλͺ¨ Class μ μμ±)
@Entity
@Inheritance (strategy = InheritanceType.SINGLE_TABLE) // μμκ΄κ³ λ§€ν μ λ΅
@DiscriminatorColumn (name = "dtype") //μ±κΈνμ΄λΈμ΄λΌ μ μ₯ν  λ κ΅¬λΆμ νκΈ°μν μλΈνμ΄μ
@Getter @Setter
public abstract class Item {

    @Id @GeneratedValue
    @Column (name = "item_id")
    private Long id;

    // κ΅¬νμ²΄κ° λ°λμ΄λ νμν κ³΅ν΅ μμ±
    private String name;
    private int price;
    private int stockQuantity;

    @ManyToMany (mappedBy = "items")  // μμ λ₯Ό μν N:N κ΄κ³
    private List<Category> categories = new ArrayList<>();
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
@DiscriminatorValue("B") // DB μ μ μ₯λ  λ κ΅¬λΆνκΈ°μν μλΈνμ΄μ (κ΄νΈκ°μ μμ°λ©΄ Class λͺμΌλ‘ μ μ₯λ¨)
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

## βοΈΒ μμ λ₯Ό μν N:N κ΄κ³

- Item
    - cartegory μ μ°κ²°νκΈ°μν List μΆκ°

```java
		@ManyToMany (mappedBy = "items")  // μμ λ₯Ό μν N:N κ΄κ³
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
    @JoinTable(name = "category item", // μ€κ°νμ΄λΈμ μ°κ²°
            joinColumns = @JoinColumn(name = "category_id"), // μ€κ° νμ΄λΈμ λ§΅ν
            inverseJoinColumns = @JoinColumn(name = "item_id") // μ€κ° νμ΄λΈμμ item μͺ½μΌλ‘ λ§΅ν
    )
    private List<Item> items = new ArrayList<>();
    // κ°μ²΄λ μκ΄μμ§λ§
    // DB λ Collection μ μμͺ½μμ κ°μ§ μ μκΈ° λλ¬Έμ
    // 1:N , N:1 λ‘ νμ΄λΌ μ μλ μ€κ° Tabel μ΄ μμ΄μΌνλ€.

    // μκΈ° μμ μ λ§΅ννλ λ°©λ² (μν μλ°©ν₯ μ°κ΄κ΄κ³ μ€μ )
    @ManyToOne(fetch = LAZY) // λΆλͺ¨λ νλμ§λ§ μμμ λ§μ μ μμΌλ―λ‘ N:1
    @JoinColumn(name = "parent_id")  // λΆλͺ¨
    private Category parent;

    @OneToMany(mappedBy = "parent") // μμ
    private List<Category> child = new ArrayList<>();
    
    //== μ°κ΄κ΄κ³ νΈμ Method ==//
    public void addChildCategory(Category child) {
        this.child.add(child);
        child.setParent(this);
    }
}
```

<br>

## βοΈΒ Table μμ±νμΈ

Entity Class λ₯Ό μ λΆ μμ±ν΄μ€ν μ»΄νμΌ μ€ννκ³  DB μ λ€μ΄κ°λ©΄ μλμΌλ‘ Table μ΄ μμ±λκ±Έ λ³Ό μ μλ€.

### πΒ μ°Έκ³ 

νλμμ Hibernate μ μν΄ DB λ‘ λ±λ‘λ  λ νλλͺμ λλ¬Έμλ₯Ό μλ¬Έμλ‘ λ°κΎΈκ³  μ¬μ΄μ _ λ₯Ό μΆκ°ν΄μ DB μ λ±λ‘ν΄μ€λ€.

- ex)

```java
private LocalDateTime orderDate;

orderDate -DB λ±λ‘-> order_date
```
