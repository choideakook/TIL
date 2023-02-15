# README

# 간단한 회원 관리 웹 어플리케이션 제작

## ✏️ 요구사항

- 회원정보
    - 이름 username
    - 나이 age
- 구현 기능
    - 회원 저장
    - 회원 목록 조회

<br>

## ✏️ 기본 세팅

### 📍 domain

```java
package com.example.servlet.domain.member;

import lombok.AccessLevel;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter @Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {

    private Long id;
    private String username;
    private int age;

    public Member(String username, int age) {
        this.username = username;
        this.age = age;
    }
}
```

<br>

### 📍 repository

- DB 없이 자체 메모리 목업에 저장하는 방식
- 싱글톤으로 객체를 생성했다.

[🔗 싱글톤](https://github.com/choideakook/TIL/blob/main/Spring/2%20Spring%20핵심원리/9%20빈%20스코프/221231%203%20Singleton%20%26%20Prototype%20함께%20사용시%20문제점.md)

```java
package com.example.servlet.domain.member;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * 동시성 문제가 고려되어있지 않음 , 실무에서는 ConcurrentHashMap, AtomicLong 등 사용을 고려해야 한다.
 */
public class MemberRepository {

    // 사실 하나만 존재시키기 위해 싱글톤으로 설정했기 때문에 static 이 없어도 된다.
    private static Map<Long, Member> store = new HashMap<>();
    private static Long sequence = 0L;

    // 싱글톤으로 운영하기 위해 instance 생성
    // 정보를 변경할 수 없도록 final 을 선선해 주어야 한다.
    private static final MemberRepository instance = new MemberRepository();

    // Class 를 호출할 때 사용되는 method
    // 오직 이 method 를 통해서만 사용할 수 있음
    public static MemberRepository getInstance() {
        return instance;
    }

    // 싱글톤은 생성자를 생성할 수 없도록 private 으로 막아주어야 함
    private MemberRepository() {}

    //-- CURD logic --//

    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    public Member findById(Long id) {
        return store.get(id);
    }

    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }

    public void clearStore() {
        store.clear();
    }
}
```

<br>

### 📍 웰컴 페이지 변경

- 경로 : main - webapp - index.html
    - 파일 전체를 새로운 코드로 변경

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body> <ul>
    <li><a href="basic.html">서블릿 basic</a></li>
    <li>서블릿
        <ul>
            <li><a href="/servlet/members/new-form">회원가입</a></li>
            <li><a href="/servlet/members">회원목록</a></li>
        </ul>
    </li>
    <li>JSP
        <ul>
            <li><a href="/jsp/members.jsp">회원목록</a></li>
        </ul>
    </li>
    <li>서블릿 MVC
        <ul>
            <li><a href="/servlet-mvc/members/new-form">회원가입</a></li>
            <li><a href="/servlet-mvc/members">회원목록</a></li>
        </ul>
    </li>
    <li>FrontController - v1
        <ul>
            <li><a href="/front-controller/v1/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v1/members">회원목록</a></li>
        </ul>
    </li>
    <li>FrontController - v2
        <ul>
            <li><a href="/front-controller/v2/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v2/members">회원목록</a></li> </ul>
    </li>
    <li>FrontController - v3
        <ul>
            <li><a href="/front-controller/v3/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v3/members">회원목록</a></li>
        </ul>
    </li>
    <li>FrontController - v4
        <ul>
            <li><a href="/front-controller/v4/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v4/members">회원목록</a></li>
        </ul>
    </li>
    <li>FrontController - v5 - v3
        <ul>
            <li><a href="/front-controller/v5/v3/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v5/v3/members">회원목록</a></li>
        </ul>
    </li>
    <li>FrontController - v5 - v4
        <ul>
            <li><a href="/front-controller/v5/v4/members/new-form">회원가입</a></li>
            <li><a href="/front-controller/v5/v4/members">회원목록</a></li>
        </ul>
    </li>
    <li>SpringMVC - v1
        <ul>
            <li><a href="/springmvc/v1/members/new-form">회원가입</a></li>
            <li><a href="/springmvc/v1/members">회원목록</a></li>
        </ul>
    </li>
    <li>SpringMVC - v2
        <ul>
            <li><a href="/springmvc/v2/members/new-form">회원가입</a></li>
            <li><a href="/springmvc/v2/members">회원목록</a></li>
        </ul>
    </li>
    <li>SpringMVC - v3
        <ul>
            <li><a href="/springmvc/v3/members/new-form">회원가입</a></li>
            <li><a href="/springmvc/v3/members">회원목록</a></li>
        </ul>
    </li>
</ul>
</body>
</html>
```