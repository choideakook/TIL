# 상품 등록 폼 - th:action

## ✏️ Controller

```java
package hello.itemservice.web.basic;

import hello.itemservice.domain.item.Item;
import hello.itemservice.domain.item.ItemRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.annotation.PostConstruct;
import java.util.List;

@Controller
@RequestMapping("/basic/items")
@RequiredArgsConstructor
public class BasicItemController {

    private final ItemRepository repository;

    //-- 모든 item 리스트 조회 --//
    @GetMapping
    public String Items(Model model) {
        List<Item> items = repository.findAll();
        model.addAttribute("items", items);
        return "basic/items";
    }

    //-- 특정 item 상세 내역 조회 --//
    @GetMapping("/{itemId}")
    public String item(@PathVariable long itemId, Model model) {
        Item item = repository.findOne(itemId);
        model.addAttribute("item", item);
        return "basic/item";
    }

    //-- 상품 등록 form --//
    @GetMapping("/add")
    public String addForm() {
        return "basic/addFrom";
    }

    //-- Test 용 init data --//
    @PostConstruct
    public void init() {
        
        repository.sava(new Item("itemA", 10000, 10));
        repository.sava(new Item("itemB", 20000, 20));
    }

}
```

<br>

## ✏️ 상품 등록 Form HTML

- ***th:action***
    - HTML Form 에서 action 의 url 이 없다면 현재 url 에 data 를 전송한다.
    - 상품 등록 폼의 url 과 실제 상품 등록을 처리하는 url 을 똑같이 맞추고 HTTP 메서드로 기능을 구분한다.

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
    <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">
    <style>
          .container {
              max-width: 560px;
          }
    </style>
</head>
<body>
<div class="container">
    <div class="py-5 text-center">
        <h2>상품 등록 폼</h2>
    </div>
    <h4 class="mb-3">상품 입력</h4>
    <!-- 상품 등록 from 과 url 이 동일하지만 method 가 다르다 -->
    <!-- 지금처럼 동일한 url 에 method 만 다를경우 action 의 url 을 생략해도 된다 -->
    <form action="item.html" th:action method="post">
        <div>
            <label for="itemName">상품명</label>
            <!-- name 에 적혀있는 값이 @RequestParam 을 통해 java 로 넘어온다. -->
            <input type="text" id="itemName" name="itemName" class="form-control" placeholder="이름을 입력하세요"></div>
        <div>
            <label for="price">가격</label>
            <input type="text" id="price" name="price" class="form-control" placeholder="가격을 입력하세요">
        </div>
        <div>
            <label for="quantity">수량</label>
            <input type="text" id="quantity" name="quantity" class="form-control" placeholder="수량을 입력하세요"></div>
        <hr class="my-4">
        <div class="row">
            <div class="col">
                <button class="w-100 btn btn-primary btn-lg" type="submit">
                    상품 등록
                </button>
            </div>
            <!-- 상품 목록으로 이동하는 링크 걸기 -->
            <div class="col">
                <button class="w-100 btn btn-secondary btn-lg"
                        onclick="location.href='items.html'"
                        th:onclick="|location.href='@{/basic/items}'|"
                        type="button">
                    취소
                </button>
            </div>
        </div>
    </form>
</div> <!-- /container -->
</body>
</html>
```