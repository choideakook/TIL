# Spring MVC - V2 Controller 통합

## ✏️ Controller 통합

지금까지 생성한 Controller 는 Class 단위이긴 하지만 사실 하나의 method 밖에 존재하지 않는다.

연관성이 있는 Controller 를 통합해 더 효율적인 유지보수를 할 수 있다.

<br>

```java
package com.example.servlet.web.springmvc.v2;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.List;

@Controller
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    // 회원 등록 폼
    @RequestMapping("/springmvc/v2/members/new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    // 회원 등록 완료 페이지
    @RequestMapping("/springmvc/v2/members/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {

        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        System.out.println("member = " + member);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }

    // 모든 회원 조회
    @RequestMapping("/springmvc/v2/members")
    public ModelAndView members() {
        List<Member> members = memberRepository.findAll();

        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);
        return mv;
    }
}
```

<br>

### 📍 URL 경로 중복 제거

- mapping 하기 위한 url 의 경로가 중복되는 문제를  `@RequestMapping` 으로 해결할 수 있다.

```java
package com.example.servlet.web.springmvc.v2;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.List;

@Controller
@RequestMapping("/springmvc/v2/members") // class 단위에 경로를 미리 입력
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form") // 논리이름만으로 mapping 을 할 수 있다.
    public ModelAndView newForm() {
        ...
    }

    @RequestMapping("/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
        ...
    }

    // class 에 있는 경로 자체가 view path 일 때는 논리이름을 적지 않아도 작동된다.
    @RequestMapping
    public ModelAndView members() {
        ...
    }
}
```