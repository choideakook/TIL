# TemplateInputException

Template / Input / Exception

템플릿 / 입력 / 예외

템플릿을 입력하는 도중 에러가 발생했다는 의미 같다.

## ✏️ 발단

wellcome page 를 만들던중 에러가 발생했다.

- controller

```java
@Controller
@Slf4j  // Logger
public class HomeController {

    @RequestMapping("/")
    public String home () {
        log.info("home controller");
        return "home";
    }
}
```

- home.html

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header">
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<div class="containera">
    <div th:replace="fragments/bodyHeader :: bodyHeader" />
    <div class="jumbotron"> <h1>HELLO SHOP</h1>
        <p class="lead">회원 기능</p> <p>
            <a class="btn btn-lg btn-secondary" href="/members/new">회원 가입</a>
            <a class="btn btn-lg btn-secondary" href="/members">회원 목록</a> </p>
        <p class="lead">상품 기능</p> <p>
            <a class="btn btn-lg btn-dark" href="/items/new">상품 등록</a>
            <a class="btn btn-lg btn-dark" href="/items">상품 목록</a> </p>
        <p class="lead">주문 기능</p> <p>
            <a class="btn btn-lg btn-info" href="/order">상품 주문</a>
            <a class="btn btn-lg btn-info" href="/orders">주문 내역</a> </p>
    </div>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->

</body>
</html>
```

- 에러 문구 요약

```html
TemplateInputException: 
			An error happened during template parsing 
						(template: "class path resource [templates/home.html]")

ParseException: 
			Error resolving template [fragments/header], 
						template might not exist or might not be accessible by any of the configured Template Resolvers

```

<br>

대략적으로 home.html 템플릿 분석중에 오류가 났고, fragments/header 를 엑세스 할 수 없다고한다.

## ✏️ 원인 분석

로그는 제대로 찍혀있기 때문에 Controller 의 문제는 아닌것같다.

- html 에서 문제가 발생했다는 의미이다.

```html
INFO 14358 --- [nio-8080-exec-1] j.jpashop.controller.HomeController : home controller
```

<br>

html 코드를 살펴보니 th:replace 의 경로와 실제 파일 경로가 다른걸 확인했다.

## ✏️ 문제 해결

실제 경로의 스펠링을 정확하게 고처주니 정상 작동 되었다.