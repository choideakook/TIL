# 상품 등록 처리 - @ModelAttribute

## ✏️ Controller

### 📍 V1

@RequestParam 을 사용해 클라이언트가 입력한 값을 객체로 변환시켜준다.

<br>

### 📍 V2

@ModelAttribute 를 사용해 Entity 를 data 운반용으로 사용하고,

어노테이션의 기능으로 Model 에 바로 값이 저장되기 때문에 Model 을 생략할 수 있다.

```java
    //-- 상품 등록 V1 --//
//    @PostMapping("/add")
    public String addItemV1(
            @RequestParam String itemName,
            @RequestParam int price,
            @RequestParam Integer quantity,
            Model model
    ) {
        Item item = new Item(itemName, price, quantity);
        Item sava = repository.sava(item);

        model.addAttribute("item", item);
        return "basic/item";
    }

    //-- 상품 등록 V2 --//
    @PostMapping("/add")
    public String addItemV2(@ModelAttribute("item") Item item) {
 
       Item sava = repository.sava(item);
       return "basic/item";
    }
```

<br>

### 📍 V3

@ModelAttribute 의 괄호도 생략할 수 있다.

- 괄호의 text 를 기준으로 model 에 값이 저장되게 되는데 괄호를 생략할 경우,
Class 명의 가장 앞의 대문자를 소문자로 변환해 저장하게 된다.
- Item → item

```java
    //-- 상품 등록 V3 --//
    @PostMapping("/add")
    public String addItemV3(@ModelAttribute Item item) {
        Item sava = repository.sava(item);
        return "basic/item";
    }
```

<br>

### 📍 V4

@ModelAttribute 자체도 생략할 수 있다.

- 맵핑 클레스의 파라미터 값이 Entity 클레스일 경우 ModelAttribute 으로 인식ㅎ

```java
    //-- 상품 등록 V4 --//
    @PostMapping("/add")
    public String addItemV4(Item item) {
        Item sava = repository.sava(item);
        return "basic/item";
    }
```