# 변경 감지와 병합 (Dirty Checking & Merge)

## ✏️ 준영속 엔티티

- JPA 영속성 Context 가 더는 관리하지 않는 Entity 를 뜻함
- 모든 DB 관련 변경, 수정, 조회 등 의 기능은 @Transactional 안에서 진행해야 영속 Entity 로써 관리받을 수 있지만 이 외부에서 임의로 DB 를 꺼내게 되면 JPA 가 관리할 수 없는 준영속 Entity 가 되버리고 만다.

<br>

[🔗 예제 보러가기](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/4%20Web%20계층%20개발/230109%206상품%20수정.md)

- itemService.saveItem(book) 에서 수정을 시도하는 Book 객체가 준영속 Entity 이다.
- Book 객체는 이미 DB 에 저장되어서 식별자 (id) 가 존재하기 때문에 새로운 id set 해줬다.
- 이렇게 임의로 만들어진 Entity 도 기존에 식별자가 있었기 때문에 준영속 Entity 로 볼 수 있다.

<br>

## ✏️ 변경 감지 Dirty Checking

기본적으로 기존 data 가 변경되면 jpa 가 자동으로 변경을 감지해 update 쿼리를 생성해 DB 에 업데이트 하게된다.

- 예시

```java
    public void cancel () {
        if (delivery.getStatus() == DeliveryStatus.COMP) {
            throw new IllegalStateException("item that have already been delivered cannot be canceled");
        }
	// 데이터가 변경되었는데 별도로 Entity 에 업데이트를 하지 않음
	// JPA 가 자동으로 변경을 감지해 DB 에 업데이트 한다.
        this.setStatus(OrderStatus.CANCEL);
        for (OrderItem orderItem : orderItems) {
            orderItem.cancel();
        }
    }
```

<br>

### 📍 변경 감지의 원리
- JPA 는 Transaction 이 Commit 되는 시점에 변경된 Entity 객체가 있는지 확인한다.  
	- 특정 Entity 객체가 변경된 경우 Update SQL 을 실행한다.  
	- Test 의 경우 마지막에 Transaction 이 rollback 되기 때문에 updata SQL 을 실행하지 않는다.  
	- Test 에서도 SQL 을 확인하고 싶으면 @Commit 어노테이션을 붙이면 확인 할 수 있다.  
  
  
### 📍 준영속 Entity

JPA 가 더이상 관리하지 않는 Entity 기 때문에 data 를 변경하더라도 변경 감지가 작동되지 않는다.  

- 그렇기 때문에 별도로 merge method 를 호출해 DB 에 update 해줘야 한다.  

```java
    @PostMapping("items/{itemId}/edit")
    public String updateItem(@ModelAttribute("form") BookForm form) {
        Book book = new Book();
        book.setId(form.getId());
        book.setName(form.getName());
        book.setPrice(form.getPrice());
        book.setStockQuantity(form.getStockQuantity());
        book.setAuthor(form.getAuthor());
        book.setIsbn(form.getIsbn());

				// Book 은 준영속 Entity 기 때문에 별도로 DB 에 입력해줘야 한다.
        itemService.saveItem(book);

        return "redirect:/items";
    }
```

- ItemService.saveItem → ItemRepository.save

```java
    public void save(Item item) {
        if (item.getId() == null) {
            em.persist(item);
        } else {
            em.merge(item);
        }
    }
```

<br>

## ✏️ 준영속 Entity 를 수정하는 방법

### 📍 1. 변경 감지 사용

이전 방법의 Controller 에서 Book 객체를 생성하며 준영속이 되므로 Service Class 에서 repository 로 id 를 찾아온다.

- 이렇게 하면 id 값을 기반으로 영속상태의 entity 를 찾아올 수 있게된다.

이후 찾아온 entity 에 BookForm 의 값들을 하나하나 넣어주면된다.

❗️ 사실 setter 도 사용하면 좋지 않기 때문에 해당 entity 에서 set 해주고 protected 로 막아놓는 방법이 가장 좋다.

```java
    @Transactional
    public void updateItem(Long itemId, String name, int price , int stockQuantity) {
        Item findId = itemRepository.findOne(itemId);
        findId.setName(name);
        findId.setPrice(price);
        findId.setStockQuantity(stockQuantity);
		}
```

- Controller 에서 update method 를 생성해주면 이전보다 간결하게 로직을 구현할 수 있다.
    - find 를 위한 id 값이 필요하기 때문에 Parameter 값에 @PathVariable Long itemId 를 추가해준다.

```java
    @PostMapping("items/{itemId}/edit")
    public String updateItem(@PathVariable Long itemId, @ModelAttribute("form") BookForm form) {

        itemService.updateItem(itemId, form.getName(), form.getPrice(), form.getStockQuantity());
        return "redirect:/items";
    }
```

이렇게 개발한 Method 는 영속성 Entity 이기 때문에 값이 변경되면 JPA 에 의해 변경 감지가 작동되고, 별도의 save Method 없이도 DB 에 자동으로 등록되게 된다.

❗️ 넘겨줄 Parameter 값이 너무 많을경우 dto class 를 생성해서 해결할 수 있다.

[🔗 dto 사용 방법]()

<br>

### 📍 2. 병합 사용 (merge)

- 병합은 준영속 상태의 Entity 를 영속 상태로 변경할 때 사용하는 기능이다.
- 이미 DB 에 저장된 식별자가 존재하는 준영속 Entity 는 사실 수정할 수없다.
    - merge 는 사용자가 입력한 새로운 data 를 기존 비영속 entity data 와 바꾸는 방법으로 수정하게 된다.
    - 즉, 수정전 준영속 Entity 와 수정후 영속성 Entity 는 전혀 다른 객체라고 할 수 있다.

<br>

### 📍 병합 사용 방식의 문제점

- merge 는 기존 data 대신 새로운 data 를 생성해 바꾸는 기능 이므로 특정 부분만 수정할 수 없다.
    - 만약 특정 부분만 수정해서 merge 할 경우 수정하지 않은 부분은 null 로 변경될 위험이 있다.
- 실무에서는 요구하는 data 가 매우 많기 때문에 merge 를 사용하는건 매우 비효율적이다.
