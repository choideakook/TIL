# GET Query Parameter

## ✏️ GET Query Parameter

메시지 바디 없이 URL 의 쿼리 파라미터를 사용해 데이터를 전달하는 방식

- 검색, 필터, 페이징 …
- ⚠️ GET 방식은 message body 가 없기 때문에 Content-type 이 없다.

### 📍 전달 데이터

- username=hello
- age=20

```
http://localhost:8080/request-param?username=hello&age=20
```

<br>

### 📍 전체 Parameter 조회 하기

URL 에서 요청한 전체 Param 값을 확인해보기

```java
package com.example.servlet.basic.request;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * 1. Parameter 전송 기능
 * http://localhost:8080/request-param?username=hello&age=20
 */
@WebServlet(name = "requestParamServlet", urlPatterns = "/request-param")
public class RequestParamServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        System.out.println("[전체 파리미터 조회] - start");

        request.getParameterNames().asIterator()
                        .forEachRemaining(
                                paramName -> System.out.println(
                                        // paramName 은 parameter 명을 출력함
                                        // request.getParameter(paramName) 은 parameter 읠 실제 값을 출력함
                                        paramName + " = " + request.getParameter(paramName)));

        System.out.println("[전체 파리미터 조회] - end");
        System.out.println();
    }
}
```

🔍 출력물 확인

- Param 명과 Param 값이 잘 출력 되었다.

```java
[전체 파리미터 조회] - start
username = hello
age = 20
[전체 파리미터 조회] - end
```

<br>

### 📍 특정 Parameter 값 조회하기

```java
System.out.println("[단일 파라미터 조회]");

String username = request.getParameter("username");
String age = request.getParameter("age");

System.out.println("username = " + username);
System.out.println("age = " + age);
System.out.println();
```

🔍 출력물 확인

```java
[단일 파라미터 조회]
username = hello
age = 20
```

<br>

### 📍 Param 이름이 동일한 복수 파라미터 조회

```java
// URL 에 username 이 다른 값으로 두번 등장함
?username=hello&age=20&username=hello2
```

전체 파라미터 조회 로직과 특정 파라미터 조회 로직을 사용하면 
첫번째로 입력했던 파라미터 값만 출력된다.

- *`request*.getParameter` 는 하나의 파라미터 이름에 대해서 단 하나의 값만 있을 때 사용해야 한다.

```java
[전체 파리미터 조회] - start
username = hello
age = 20
[전체 파리미터 조회] - end

[단일 파라미터 조회]
username = hello
age = 20
```

<br>

- 복수 파라미터 값을 조회하는 로직
    - 값을 배열로 만든 후 for 문을 통해 모두 출력해주면 모든 값을 출력할 수 있다.
    - 이 경우엔 *`request*.getParameterValues` 을 사용해야 한다.

```java
System.out.println("[이름이 같은 복수 파라미터 조회]");
        String[] usernames = request.getParameterValues("username");
        for (String name : usernames) {
            System.out.println("username = " + name);
        }
```

🔍 출력물확인

```java
[이름이 같은 복수 파라미터 조회]
username = hello
username = hello2
```