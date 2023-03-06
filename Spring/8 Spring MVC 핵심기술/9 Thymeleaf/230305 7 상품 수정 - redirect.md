# 상품 수정 - redirect

## ✏️ Controller

@PathVariable 로 수정하려는 id 를 파싱하고 수정한다.

같은 url 을 매핑하고 method 로 요청을 구별한다.

```java
    //-- 상품 수정 폼 --//
    @GetMapping("/{itemId}/edit")
    public String editForm(@PathVariable Long itemId, Model model) {
        Item item = repository.findOne(itemId);
        model.addAttribute("item", item);
        return "basic/editForm";
    }

    //-- 상품 수정 --//
    @PostMapping("/{itemId}/edit")
    public String edit(@PathVariable Long itemId, @ModelAttribute Item item) {
        repository.update(itemId, item);
        // 리다이렉트를 이용해 등록에 성공하면 상세 페이지로 이동한다.
        // 리다이렉트 에서도 PathVariable 을 사용할 수 있다.
        return "redirect:/basic/items/{itemId}";
    }
```

### ⚠️ HTML form 전송은 PUT, PATCH 를 지원하지 않기 때문에 사용할 수 없다.

- 해당 method 는 Rest API 에서만 사용된다.
- HTML form 에서는 GET 과 POST 만 사용 가능하다.

<br>

## ✏️ 상품 수정 HTML

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
    <div class="py-5 text-center"><h2>상품 수정 폼</h2>
    </div>
    <!-- action 설정 -->
    <form action="item.html" th:action method="post">
        <div>
            <label for="id">상품 ID</label> 
            <!-- values 에 model 에서 가져올 값을 입력함 --> 
            <input type="text" id="id" name="id" class="form-control" th:value="${item.id}" readonly>
        </div>
        <div>
            <label for="itemName">상품명</label>
            <input type="text" id="itemName" name="itemName" class="form- control" th:value="${item.itemName}">
        </div>
        <div>
            <label for="price">가격</label>
            <input type="text" id="price" name="price" class="form-control" th:value="${item.price}">
        </div>
        <div>
            <label for="quantity">수량</label>
            <input type="text" id="quantity" name="quantity" class="form-control" th:value="${item.quantity}">
        </div>
        <hr class="my-4">
        <div class="row">
            <div class="col">
                <button class="w-100 btn btn-primary btn-lg" type="submit">
                    저장
                </button>
            </div>
            <div class="col"> <!-- 취소 할 경우 상품 상세로 돌아가게 설정 -->
                <button class="w-100 btn btn-secondary btn-lg"
                        onclick="location.href='item.html'"
                        th:onclick="|location.href='@{/basic/items/{itemId}(itemId=${item.id})}'|"
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