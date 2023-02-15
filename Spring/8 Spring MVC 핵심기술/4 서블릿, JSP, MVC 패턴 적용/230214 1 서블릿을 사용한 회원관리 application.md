# 서블릿을 사용한  회원관리 application

## ✏️ 회원 등록 폼

- MemberFormServlet
    - Java 코드를 통해 HTML 을 작성해야 하기 때문에 매우 불편하다.

[🔗 HTML 형식으로 응답](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/3%20HTTP%20요청과%20응답/230213%206%20HTTP%20응답%20Data.md)

```java
package com.example.servlet.web.servlet;

import com.example.servlet.domain.member.MemberRepository;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name = "memberFromServlet",urlPatterns = "/servlet/members/new-form")
public class MemberFromServlet extends HttpServlet {

    // instance 생성
    MemberRepository repository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //-- HTTP 응답 message --//
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        //-- HTTP 응답 message body --//
        PrintWriter writer = response.getWriter();

        // 이제 html 을 작성해야하는데
        // java 코드를 통해 html 을 작성해야하기 때문에
        // 이 방법은 매우 불편하고 오타의 확률이 매우 크다.
        writer.write("<!DOCTYPE html>\n" +
                "<html>\n" +
                "<head>\n" +
                "    <meta charset=\"UTF-8\">\n" +
                "    <title>Title</title>\n" +
                "</head>\n" +
                "<body>\n" +
                "<form action=\"/servlet/members/save\" method=\"post\">\n" +
                "    username: <input type=\"text\" name=\"username\" />\n" +
                "    age:      <input type=\"text\" name=\"age\" />\n" +
                "    <button type=\"submit\">전송</button>\n" +
                "</form>\n" +
                "</body>\n" +
                "</html>\n");
    }
}
```

<br>

## ✏️ 회원 저장 페이지

- memberSaveServlet
    - form 에서 클라이언트가 입력한 정보를 param 으로 받아 객체로 변환한다.
    - 변환된 객체를 db 에 저장하고 저장된 결과를 html 로 출력하는 코드를 응답한다.
    - 응답된 코드가 클라이언트의 웹에 출력된다.

```java
package com.example.servlet.web.servlet;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(name = "memberSaveServlet", urlPatterns = "/servlet/members/save")
public class MemberSaveServlet extends HttpServlet {

    // instance 생성
    MemberRepository repository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // 정상적으로 호출되었는지 확인하기 위한 출력 로직
        System.out.println("MemberSaveServlet.service");

        //-- 클라이언트가 요청한 정보를 객체로 변환하는 작업 --//
        String username = request.getParameter("username");
        // getParameter 의 변수값은 항상 String 이기 때문에 int 로 캐스팅을 해주어야 한다.
        int age = Integer.parseInt(request.getParameter("age"));

        // 변환된 객체를 통해 DB 에 저장
        Member member = new Member(username, age);
        repository.save(member);

        //-- 응답 HTTP message --//
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        // 응답 message body
        // 중간의 member 에서 가져온 get method 덕분에
        // 동적 html 코드가 되었다.
        PrintWriter w = response.getWriter();
        w.write("<html>\n" +
                "<head>\n" +
                "    <meta charset=\"UTF-8\">\n" +
                "</head>\n" +
                "<body>\n" +
                "성공\n" +
                "<ul>\n" +
                "    <li>id="+member.getId()+"</li>\n" +
                "    <li>username="+member.getUsername()+"</li>\n" +
                "    <li>age="+member.getAge()+"</li>\n" +
                "</ul>\n" +
                "<a href=\"/index.html\">메인</a>\n" +
                "</body>\n" +
                "</html>");
    }
}
```

<br>

## ✏️ 저장된 모든 회원 정보 조회 페이지

```java
package com.example.servlet.web.servlet;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.List;

@WebServlet(name = "memberListServlet", urlPatterns = "/servlet/members")
public class MemberListServlet extends HttpServlet {

    MemberRepository repository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        List<Member> members = repository.findAll();

        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        PrintWriter w = response.getWriter();
        w.write("<html>");
        w.write("<head>");
        w.write("    <meta charset=\"UTF-8\">");
        w.write("    <title>Title</title>");
        w.write("</head>");
        w.write("<body>");
        w.write("<a href=\"/index.html\">메인</a>");
        w.write("<table>");
        w.write("    <thead>");
        w.write("    <th>id</th>");
        w.write("    <th>username</th>");
        w.write("    <th>age</th>");
        w.write("    </thead>");
        w.write("    <tbody>");
        
        // 동적 HTML 을 위한 For 문
        for (Member member : members) {
            w.write("    <tr>");
            w.write("        <td>" +member.getId()+ "</td>");
            w.write("        <td>" +member.getUsername()+ "</td>");
            w.write("        <td>" +member.getAge()+ "</td>");
            w.write("    </tr>");
        }

        w.write("    </tbody>");
        w.write("</table>");
        w.write("</body>");
        w.write("</html>");
    }
}
```

<br>

## ✏️ 정리

회원 정보 등록부터 조회, 모든 회원 리스트 조회까지 정상적으로 작동되지만,

응답 message body 의 html 작성 방식이 매우 까다롭고 유지보수 관점에서도 좋지 못하다.

Template 앤진을 사용하면 이러한 문제점을 개선할 수 있다.

- 대표적인 Template 앤진
    - JSP, Thymleaf, Freemarker, Velocity …