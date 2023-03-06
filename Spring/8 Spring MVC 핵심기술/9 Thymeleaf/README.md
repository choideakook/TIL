# README

# 📚 Create Projcet 📚

[🔗 완성된 코드 보기](https://github.com/choideakook/simple-SSR-CRUD-project)

## ✏️ Option

- Spring Boot 2.7.9
- Group : hello
- Artifact : item-service
- Package name : hello.itemservice
- Packaging : Jar
- Java 11
- Dependency
    - Spring Web
    - Thymeleaf
    - Lombok
    - Validation

<br>

## ✏️ Welcome page

- static/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>상품 관리
        <ul>
            <li><a href="/basic/items">상품 관리 - 기본</a></li>
        </ul>
    </li>
</ul>
</body>
</html>
```

<br>

## ✏️ 요구사항 분석

상품을 관리할 수 있는 서비스를 만들어보자.

- ***상품 도메인 모델***
    - 상품 id
    - 상품 이름
    - 가격
    - 수량
- ***상품 관리 기능***
    - 상품 목록
    - 상품 상세
    - 상품 등록
    - 상품 수정

### 📍 서비스 제공 흐름

![s8911.png](README%20fc43e6d2585e4b3688e2ebe2f5d63f4d/s8911.png)