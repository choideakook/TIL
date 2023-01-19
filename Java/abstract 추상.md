# abstract 추상

상속을 강제하는 일종의 규제

- abstract Class 나 Method 를 사용하기 위해선 반드시 상속해서 사용해야 한다.

## ✏️ abstract 기본 원리

- 만약 abstract Class 를 직접 사용하게 되면 error 가 발생하게 된다.

```java
abstract class Abstract1{
    public int a();
}

public class ADemo{
		public static void main(String[] args){
        // 추상 Class 를 바로 사용했기 때문에 에러가 발생됨
        Abstract1 test = new Abstract1();
    }
}
```

- 상속을 하고 상속한 Class 를 사용하면 error 가 없어짐

```java
abstract class Abstract1{
    public int a();
}

class Abstract2 extends Abstract1 {
}

public class ADemo{
		public static void main(String[] args){
        // error 가 해결됨
        Abstract2 test = new Abstract2();
    }
}
```

- 마찬가지로 abstract Method 도 상속을 통해야만 사용이 가능하다.
    - Class 내의 Method 들 중 하나라도 abstract Method 일 경우 해당 Class 는 abstract Class 가 되게 된다.

```java
abstract class Abstract1 {
    // abstract Method 는 중괄호 안에 구체적인 내용을 입력할 수 없음
    public abstract int a();
}

class Abstract2 extends Abstract1 {
    // method 를 구현하지 않으면 error가 발생됨
    public int a(){
        return 1;
    }
}

public class ADemo{
		public static void main(String[] args){
        Abstract2 test = new Abstract2();
    }
}
```

<br>

## ✏️ abstract 를 사용하는 이유

비슷하지만 다른 여러가지 기능을 구현해야 할 경우 추상 클래스로 틀을 잡아놓고 상속 클래스에서 구체적인 기능을 구현할 수 있다.

- 추상 클래스 Item
    - 상속 class 들이 공통되는 필드만 생성
    - setter 를 사용하지 않고 필드값을 input 할 수 있음

```java
@Entity
@Getter
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn (name = "dtype")
public abstract class Item {

    @Id @GeneratedValue
    @Column (name = "item_id")
    private Long id;

    private String name;

    private int price;

    private int stockQuantity;

    //== input logic ==//
    public void createItem(String name, int price, int stockQuantity) {
        this.name = name;
        this.price = price;
        this.stockQuantity = stockQuantity;
    }
```

- 상속 클래스 Jelly 와 Snack
    - 상속 클래스 만의 특징이 되는 필드를 생성
    - 마찬가지로 setter 를 사용하지 않음

```java
package smallmall.smallmall.domain.item;

import lombok.Getter;

import javax.persistence.*;

@Entity
@Getter
@DiscriminatorValue("Jelly")
public class Jelly extends Item{

    private String shape;
    private String taste;

    public void createSignature(String shape, String taste) {
        this.shape = shape;
        this.taste = taste;
    }
}

@Entity
@Getter
@DiscriminatorValue("Snack")
public class Snack extends Item{

    private String seasoning;
    private String snackType;

    public void createSignature(String seasoning, String snackType) {
        this.seasoning = seasoning;
        this.snackType = snackType;
    }
}
```

- 추상 클래스 Item 은 사용할 수 없지만 상속 클래스 Jelly 를 생성해 Item 의 method 를 사용할 수 있음

```java
@Test
    void createOrder() {
        // entity input
        Member member = new Member("MemberA", new Address("010", "seoul"));
        // Jelly 를 생성해 필드값을 input 해 setter 없이 정상 작동시킴
        Jelly jelly = new Jelly();
        int itemPrice = 1000;
        jelly.createItem("apple jelly", itemPrice, 5);
        jelly.createSignature("worm", "apple");

        // save
        Long memberId = memberService.join(member);
        Long jellyId = itemService.create(jelly);

        //create order
        int orderCount = 3;
        Long orderId = orderService.createOrder(memberId, jellyId, orderCount);
        Order findOrder = orderService.findOne(orderId);

        // testing
        assertThat(findOrder.getMember()).isSameAs(memberService.findOne(memberId));
        assertThat(jelly.getStockQuantity()).isEqualTo(2);
        assertThat(findOrder.getStatus()).isEqualTo(OrderStatus.ORDER);
        assertThat(findOrder.getOrderItems().size()).isEqualTo(1);
        assertThat(findOrder.getTotalPrice()).isEqualTo(itemPrice * orderCount);
    }
```