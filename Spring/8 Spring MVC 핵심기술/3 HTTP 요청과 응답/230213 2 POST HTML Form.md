# POST HTML Form

## ✏️ POST HTML Form

HTML 의 form 을 사용해 클라이언트에서 서버로 data 를 전달하는 방식

- 회원가입, 상품 주문 …

### 📍 특징

- content-type: application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파리미터 형식으로 데이터를 전달한다.
    - username=hello&age=20

### 📍 HTML 만들기

클라이언트가 요청값을 입력할 수 있게 text box 가 있는 html 파일을 생성해준다.

- 파일 위치 : basic.hello-form.html
- text box 에 입력된 값은 이전시간에 GET 방식으로 만든 로직으로 보낸다.
    - GET 방식과 POST 방식은 Parameter 를 같은 방식으로 전달하기 때문에 호환이 가능하다.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!-- text box 의 입력 결과물은 /request-param 로 전달된다. -->
<form action="/request-param" method="post">
    username: <input type="text" name="username" />
    age: <input type="text" name="age" />
    <button type="submit">전송</button>
</form>
</body>
</html>
```

⚠️ url 은 단순하게 html 파일이 위치한 디렉토리를 입력해도 웹에서 페이지를 출력해준다.

- http://localhost:8080/basic/hello-form.html

🔍 출력물 확인

- web pager 가 http://localhost:8080/request-param 로 이동하고 파라미터가 정상적으로 출력 되었다.

```html
[전체 파리미터 조회] - start
username = kim
age = 20
[전체 파리미터 조회] - end

[단일 파라미터 조회]
username = kim
age = 20

[이름이 같은 복수 파라미터 조회]
username = kim
```

<br>

## ✏️ 정리

- `content-type: application/x-www-form-urlencoded` 형식은 GET 에서 사용한 Query Parameter 형식과 같다.
    - 즉, Query Parameter 조회 method 를 그대로 사용할 수 있다.
- 클라이언트의 입장에서 두 방식의 차이가 있지만,
서버 입장에서는 두 방식의 형식이 동일하므로 두 방식을 구분할 필요가 없다.
    - 즉, *`request*.getParameter()` 는 GET URL  Query Parameter 형식도 지원하고,
    POST HTML Form 형식도 둘 다 지원한다.

⚠️ GET 의 URL 쿼리 파라미터 형식은 HTTP body 를 사용하지 않기 때문에 Content-type 이 없다.

<br>

## ✏️ Post man 을 사용한 Test

매번 서버가 잘 작동하는지 확인하기 위해 html 파일을 만들지 않아도,

post men 을 사용해 편하게 test 를 할 수 있다.

[🔗 post man 사용방법](https://github.com/choideakook/TIL/blob/main/Spring/4%20JPA%20활용2/1%20API%20개발%20기본/230111%201%20회원%20등록%20API.md)

![s8261.png](POST%20HTML%20Form%20f768997abc2f4539862afc65fc58917c/s8261.png)