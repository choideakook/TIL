# 상품 목록 - Thymeleaf 기본기능

Controller 로직을 생성 후,

이전시간에 만들었던 정적 html 파일을 복붙해 Thymeleaf 를 사용해 동적 html 로 변환 시켜준다.

## ✏️ Controller

```java
package hello.itemservice.web.basic;

import hello.itemservice.domain.item.Item;
import hello.itemservice.domain.item.ItemRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.annotation.PostConstruct;
import java.util.List;

@Controller
@RequestMapping("/basic/items")
@RequiredArgsConstructor
public class BasicItemController {

    private final ItemRepository repository;

    //-- 모든 item 조회 --//
    @GetMapping
    public String Items(Model model) {
        List<Item> items = repository.findAll();
        model.addAttribute("items", items);
        return "basic/items";
    }

    //-- Test 용 init data --//
    @PostConstruct
    public void init() {
        repository.sava(new Item("itemA", 10000, 10));
        repository.sava(new Item("itemB", 20000, 20));
    }
}
```

### 📍 모든 Item 조회 - template/basic/items.html

- 항상 html 의 속성 기능과 thymeleaf 의 속성 기능이 동시에 존재할경우 html 의 기능이 무시되고 thymeleaf 의 기능으로 작동된다.
    - 서버를 통하지 않고 HTML 을 그대로 보면 타임리프의 기능이 적용 되지 않는다.
- ***th:xxx***
    - 타임리프의 속성 문법
    - 대부분의 HTML 속성을 타임리프 속성으로 변환할 수 있다.
- ***${…}***
    - 변수 표현식
    - Model 에 포함된 값이나, 타임리프 변수로 선언한 값을 조회할 수 있다.
    - property 접근 법을 사용해 java 객체 처럼 사용할 수 있다.
    
    ```html
    <td th:text="${item.quantity}">10</td>
    ```
    
- ***@{…}***
    - 타임리프의 링크 표현식
    - 표현식 내부에 변수표현식을 사용해 PathVariable url 을 생성할 수 있다.
        - 경로 변수방식을 사용한 링크 만들기
    
    ```html
    <a th:href="@{/basic/items/{itemId}(itemId=${item.id})}" th:text="${item.id}">회원 id</a>
    ```
    
    - 경로 변수뿐 아니라 쿼리 파라미터도 생성할 수 있다.
    
    ```html
    <a th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}">
    
    => 생성된 링크 http://localhost:8080/basic/items/1?query=test
    ```
    
- **|…|**
    - 리터럴 대체 문법
    - 리터럴 대체 문법이 없다면 Spring 의 System.out.print() 처럼 연산자를 사용해 data 를 입력해야 한다.
    
    ```html
    <span th:text="'Welcome to our application, ' + ${user.name} + '!'">
    ```
    
    - 리터럴 대체 문법을 사용하면 연산자 없이 한번에 data 를 입력할 수 있다.
    
    ```html
    <span th:text="|Welcome to our application, ${user.name}!|">
    ```
    
    - 단순 text 뿐 아니라 링크도 리터럴 대체 문법을 사용해 편리하게 작성할 수 있다.
    
    ```html
    // 리터럴 사용 X
    <a th:href="@{/basic/items/{itemId}(itemId=${item.id})}" th:text="${item.id}">회원 id</a>
    
    // 리터럴 사용 O
    <a th:href="@{|/basic/items/${item.id}|}" th:text="${item.itemName}">상품 명</a>
                   
    ```
    
    - button 일 경우 리터럴 링크 사용법
        - 리터럴 안에 리터럴을 한번 더 사용하는 중첩은 인식하지 못한다.
    
    ```html
    <button type="button" th:onclick="|location.href='@{/basic/items/{itemId}/edit(itemId=${item.id})}'|">
    ```
    
- ***th:each***
    - 반복 출력 문법
    - model 에서 data 를 꺼내 반복문을 사용할 수 있다.
    - Collection 의 숫자 만큼 <tr>…</tr> 도 반복되어 사용된다.
    
    ```html
    <tr th:each="item : ${items}">
    ```
    
- ***타임리프를 적용한 HTML 코드***

```html
<!DOCTYPE HTML>
<!-- 타임리프를 사용하기 위한 선언 -->
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="utf-8">
    <!-- 타임리프용 css 선언 방식 -->
    <link th:href="@{/css/bootstrap.min.css}" rel="stylesheet">
</head>
<body>

<div class="container" style="max-width: 600px">
    <div class="py-5 text-center">
        <h2>상품 목록</h2>
    </div>

    <div class="row">
        <div class="col">
<!-- 타임리프식 링크 걸기 : html onclick 속성이 이미 있지만 SSR 의경우 타임리프의 속성만 적용됨-->
            <button class="btn btn-primary float-end"
                    onclick="location.href='addForm.html'"
                    th:onclick="|location.href='@{/basic/items/add}'|"
                    type="button">상품 등록</button>
        </div>
    </div>

    <hr class="my-4">
    <div>
        <table class="table">
            <thead>
            <tr>
                <th>ID</th>
                <th>상품명</th>
                <th>가격</th>
                <th>수량</th>
            </tr>
            </thead>
            <tbody>
            <!-- 타임리프식 루프 문법 -->
            <tr th:each="item : ${items}">
                <!-- data 를 입력하는 방법과 PathVariable 링크 거는 방법 -->
                <td><a th:href="@{/basic/items/{itemId}(itemId=${item.id})}" th:text="${item.id}">회원 id</a></td>
                <td><a th:href="@{|/basic/items/${item.id}|}" th:text="${item.itemName}">상품 명</a></td>
                <td th:text="${item.price}">10000</td>
                <td th:text="${item.quantity}">10</td>
            </tr>
            </tbody>
        </table>
    </div>
</div> <!-- /container -->
</body>
</html>
```