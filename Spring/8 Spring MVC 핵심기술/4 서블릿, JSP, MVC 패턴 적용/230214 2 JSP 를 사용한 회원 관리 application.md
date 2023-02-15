# JSP 를 사용한 회원 관리 application

## ✏️ JSP 세팅

### 📍 라이브러리 추가

- dependencies

```java
//JSP 추가 시작
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'javax.servlet:jstl'
//JSP 추가 끝
```

<br>

### 📍 새로운 디렉토리 생성

- JSP 는 Java 형식이 아니기 때문에 main.java 디렉토리에 위치할 수 없다.
- webapp 에 jsp 디렉토리를 새로 생성해서 작업해야한다.
    - webapp.jsp.members

<br>

## ✏️ 회원 등록 폼

- 디렉토리 위치인 jsp/members/new-form.jsp 로 접속하면 페이지가 출력된다.

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/jsp/members/save.jsp" method="post">
    username: <input type="text" name="username" />
    age: <input type="text" name="age" />
    <button type="submit">전송</button>
</form>
</body>
</html>
```

<br>

## ✏️ 회원 저장 페이지

- java 코드를 가져와서 사용할 수 있음
    - instance 생성과 import 도 java 와 동일하게 해야한다.
    - request 와 response 는 자동으로 사용가능하다.

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page import="hello.servlet.domain.member.MemberRepository" %>
<%
    MemberRepository repository = MemberRepository.getInstance();

    System.out.println("MemberSaveServlet.service");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));

    Member member = new Member(username, age);
    repository.save(member);
%>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
성공
  <ul>
      <li>id=<%=member.getId()%></li>
      <li>username=<%=member.getUsername()%></li>
      <li>age=<%=member.getAge()%></li>
</ul>
<a href="/index.html">메인</a>
</body>
</html>
```

<br>

## ✏️ 정리

HTML 코드를 이전보다 편하게 사용할 수는 있게됬지만,

아직 너무 불편하다.

java 코드와 html 코드 모두 유지보수가 힘들어졌고, 오타가 발생될 확률이 너무 높다.