# Spring MVC - V1 시작하기

Spring 이 제공하는 Controller 는 어노테이션 기반으로 동작하기 때문에 매우 유연하고 실용적이다.

## ✏️ Spring MVC 적용

### 📍 @RequestMapping

Spring MVC 의 핵심 어노테이션이다.

`RequestMappingHandlerMapping` 과 `RequestMappingHandlerAdapter` 를 지원하고
이 둘 앞의 두개의 단어에서 따와 만들어진 어노테이션이다.

- 실무에서는 대부분 @RequestMapping 을 사용한 Controller 가 사용된다.

<br>

### 📍 회원 등록 폼

- Class 에 Controller 어노테이션을 사용한다.
    - @Controller 가 있으면 Spring 이 자동으로 Spring Bean 으로 등록한다.
    - @Controller 가 있으면 Spring MVC 에서 어노테이션 기반 Controller 로 인식한다.
        - `RequestMappingHandlerMapping` 에서 `Handler` 정보로 인식하게 된다.
- 회원 등록 폼 url 을 매핑할 method 에 @RequestMapping 어노테이션을 사용한다.
    - 해당 url 로 요청되는 http 메시지를 매핑해 method 가 실행된다.
    - 반환 값 view path 는 논리 이름만 적어주어도 된다.
        - application.properties 에 경로 설정을 해두어서 가능하다.

⚠️ Class 에 @Controller 어노테이션을 붙이는 이유

- `RequestMappingHandlerMapping` 는 Spring Bean 에 등록된 객체중`@RequestMapping` 가 있는 객체를 찾아 `Handler` 정보로 인식한다.
    - Method 는 Spring Bean 에 등록이 될 수 없으므로 Class 에 어노테이션을 붙여주어야 한다.
- 꼭 `@Controller` 를 사용하지 않아도 조건만 만족시켜주면 똑같은 기능을 작동시킬 수 있다.
    - `@Conponen` + `@RequestMapping` 어노테이션을 같이 사용해도 `@Controller` 와 같은 기능이 작동된다.
    - `@Conponen` 없이 직접 수동으로 Bean 등록을 해도 같은 기능이 작동된다.
    - ❗spring 버전 3.x.x 부터는 `@Controller` 와 `@RestController` 만 인식되고, `@RequestMapping` 는 인식되지 않는다.

```java
package com.example.servlet.web.springmvc.v1;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class SpringMemberFormControllerV1 {

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
}
```

<br>

### 📍 회원 등록 완료 페이지

```java
package com.example.servlet.web.springmvc.v1;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Controller
public class SpringMemberSaveControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v1/members/save")
    public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {

        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        System.out.println("member = " + member);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }
}
```

<br>

### 📍 모든 회원 조회

```java
package com.example.servlet.web.springmvc.v1;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import java.util.List;

@Controller
public class SpringMemberListControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v1/members")
    public ModelAndView process() {
        List<Member> members = memberRepository.findAll();

        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);
        return mv;
    }
}
```