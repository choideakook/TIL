# if문 & PRG - POST Redirect GET

## ✏️ Redirect 의 필요성

[🔗 Redirect](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%202%203XX%20Redirction.md)

상품을 등록후 나타나는 상세페이지에서 새로고침할 경우 동일한 상품이 계속해서 생성되는 문제가 발생한다.

- 상품 상세 → 상품 등록 → 새로고침 → 상품 무한 생성
- 새로고침은 최근에 요청한 url 을 다시 요청하는 기능이므로 새로고침 할경우 post 요청에 계속해서 발생해 상품이 등록되게 된다.

<br>

## ✏️ PRG 전략으로 문제 해결

POST 로 등록 , 수정을 완료한 응답에 새로고침할경우 다시 같은 POST 명령이 발생되므로,
Redirect 를 통해 최근 명령을 POST 가 아닌 GET 으로 변경시켜 준다.

- Redirect 를 할 경우 return 의 경로에 있는 html 을 보여주는 것이 아닌,
Redirect 의 경로에 있는 html 을 GET 으로 요청하게 된다.
- 즉, POST 이후에 GET 요청이 즉시 발생하게 된다.

### 📍 PRG 적용

상품 등록후 새로고침을 해도 중복 등록이 되지 않는다.

```java
    //-- 상품 등록 V5 : redirect --//
    @PostMapping("/add")
    public String addItemV5(Item item) {
        Item sava = repository.sava(item);

        return "redirect:/basic/items/" + item.getId();
    }
```

<br>

## ✏️ RedirectAttributes

### 📍 필요성

지금까지는 상품 상세페이지를 상품 상세페이지로, 상품 등록 성공 페이지로 2가지 용도로 사용하고 있다.

하지만 클라이언트 입장에서 상품 등록을 완료하고 상품 상세페이지로 도착했을 때 별도의 안내 문구가 없어 목록에서 확인을 하기 전까지는 제대로 등록되었는지 추측할 수 밖에 없다.

<br>

### 📍 RedirectAttributes 로 문제 해결

```java
    //-- 상품 등록 V6 : RedirectAttributes --//
    @PostMapping("/add")
    public String addItemV6(Item item, RedirectAttributes redirectAttributes) {
        Item savaItem = repository.sava(item);
        // RedirectAttributes 에 입력한 itemId 를 return 에서도 사용할 수 있게된다.
        redirectAttributes.addAttribute("itemId", savaItem.getId());
        // url 에 포함되지 않은 객체는 Query Parameter 형식으로 전달 된다.
        // ex) ?status=true
        // 만약 itemId 도 return 에 없었다면 status 와 함께 파라미터로 전달된다.
        redirectAttributes.addAttribute("status", true);
        return "redirect:/basic/items/{itemId}";
    }
```

<br>

### 📍 item.html

제목 바로 밑에 타임리프의 if 문을 사용해 true 일때만 나타나는 text 로직을 만들었다.

- ***th:if=”…”***
    - 타임리프의 if 문 쌍따옴표안의 내용이 true 일 때 실행된다.
- ***th:text “’…’”***
    - 타임리프의 태그로, 쌍따옴표 안에 따옴표를 넣어주면 text 를 바로 입력할 수 있다.

```html
<body>
<div class="container">
    <div class="py-5 text-center">
        <h2>상품 상세</h2></div>
    <div>
        <!-- 타임리프식 조건문 -->
        <h2 th:if="${param.status}" th:text="'저장 완료'"></h2>

        <label for="itemId">상품 ID</label>
        <input type="text" id="itemId" name="itemId" class="form-control" th:value="${item.id}" readonly>
    </div>
```