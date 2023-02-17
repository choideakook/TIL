# Spring MVC - V3 실용적인 방식

MVC 프레임워크를 직접 만들 때 V3 는 ModelView 를 반환했지만,
V4 는 논리이름만 반환했었다.

[🔗 MVC 프레임워크 만들기 V3](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/5%20MVC%20프레임워크%20만들기/230215%202%20V3%20-%20Model%20추가.md)

[🔗 MVC 프레임워크 만들기 V4](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/5%20MVC%20프레임워크%20만들기/230215%203%20V4%20-%20단순하고%20실용적인%20Controller.md)

Spring MVC 에서도 논리이름만 반환하는 방식으로 사용할 수 있다.

<br>

## ✏️ 적용하기

- 단순히 method 의 반환을 ModelView 에서 String 으로 변경해주면 된다.
    - return 값도 논리이름으로 반환시키면 된다.
    - Spring 에서 제공하는 다양한 adapter 덕분에 별도의 adapter 생성 없이 적용할 수 있다.
- 요청 parameter 값 가져오기
    - @RequestParam 어노테이션을 사용하면 원하는 parameter 를 직관적으로 사용할 수 있다.
- model 에 저장해야 하는 business logic 의 결과물은 Spring 이 판단할 수 없으므로 직접 넘겨주어야 한다.
    - Param 에  Model 을 받아서 *`model*.addAttribute()` 로 직접 저장할 수 있다.

```java
package com.example.servlet.web.springmvc.v3;

import com.example.servlet.domain.member.Member;
import com.example.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import java.util.List;

@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form")
    public String newForm() {
        return "new-form";
    }

    @RequestMapping("/save")
    public String save(
            // 요청 param값 변수화
            @RequestParam("username") String username,
            @RequestParam("age") int age,
            // 결과값을 model 에 저장하기 위한 객체
            Model model) {

        // 요청 param 값을 편리하게 사용할 수 있다.
        Member member = new Member(username, age);
        System.out.println("member = " + member);
        memberRepository.save(member);

        // Model 에 저장할 business logic 의 결과
        model.addAttribute("member", member);

        // 논리이름 반환
        return "save-result";
    }

    @RequestMapping
    public String members(Model model) {

        List<Member> members = memberRepository.findAll();

        model.addAttribute("members", members);
        return "members";
    }
}
```

<br>

## ✏️ V3 의 문제점

[🔗 HTTP method](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/2%20HTTP%20개념과%20메서드/230121%201%20HTTP%20Method.md)

- HTTP method 를 구별하지 못하고 모든 URL 만 맞다면 모든 HTTP method 를 맵핑한다.
    - 클라이언트의 요청이 등록인가, 조회인가 등등
    - method 로 구별해야 하는 내용을 구별할 수 없다.

### 📍 V3 의 문제 개선 1.

[🔗 HTTP status](https://github.com/choideakook/TIL/blob/main/Spring/5%20HTTP%20웹%20기본%20지식/3%20HTTP%20상태코드/230125%203%204xx%20-%20클라이언트%20오류.md)

- 어노테이션의 설정에 논리명과 HTTP method 를 같이 입력하는 방법으로 개설할 수 있다.
    - HTTP method 를 명시했기 때문에 만약 POST 로 요청이 올경우 405 method not allowed 에러가 발생한다.

```java
@RequestMapping(value = "/new-form", method = RequestMthod.GET)
    public String newForm() {
        return "new-form";
    }
```

- 이 방식은 가독성이 좋지않고 작성하기 번거로운 문제를 갖고있다.
    - 특정 HTTP mehod 만을 매핑하는 어노테이션을 사용해 문제를 개선할 수 있다.

<br>

### 📍 V3 의 문제 개선 2.

- 특정 HTTP method 만을 매핑하는 어노테이션으로 가독성과 사용 편의성이 크게 증가했다.

```java
package com.example.servlet.web.springmvc.v3;

import ...

@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @GetMapping("/new-form")
    public String newForm() {
        return "new-form";
    }

    @PostMapping("/save")
    public String save(
            @RequestParam("username") String username,
            @RequestParam("age") int age,
            Model model) {

        Member member = new Member(username, age);
        System.out.println("member = " + member);
        memberRepository.save(member);

        // Model 에 저장할 business logic 의 결과
        model.addAttribute("member", member);

        return "save-result";
    }

    @GetMapping
    public String members(Model model) {

        List<Member> members = memberRepository.findAll();

        model.addAttribute("members", members);
        return "members";
    }
}
```