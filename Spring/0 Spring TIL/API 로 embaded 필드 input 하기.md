# API 로 embaded 필드 input 하기

JPI 활용 2 편 에서는 편의상 Member Enitity 의 name 값만 input 해서 DB 에 등록했고,

완강후 기록한 내용을 바탕으로 복습하던중 embaded Class 의 필드값까지 DB 에 등록하려고 했다.

❗️ @Setter 없이

[🔗 김영한님의 JPI 활용 2 편 회원 등록](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/4%20Web%20계층%20개발/230109%203%20회원%20등록.md)

<br>

## ✏️ 등록하는 방법

강의 내용과 다르게 Entity 에 Setter 를 최대한 넣지 않으려고 노력했고,

생성자를 통해서 값을 주입하는 방식으로 만들었다.

- Member Entity
    - 처음엔 생성자에 address Parameter 까지 넣었지만 API 부분에서 지식의 한계로 address 를 주입하는 method 를 별도로 만들어주었다.

```java
package smallmall.smallmall.domain;

import lombok.Getter;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;

@Entity
@Getter
public class Member {

    @Id @GeneratedValue
    @Column (name = "member_id")
    private Long id;

    private String name;

    @Embedded
    private Address address;

    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();

    //== set Member logic ==//
    public Member(String name) {
        this.name = name;
    }

    public void setAddress(String phone, String myAddress) {
        this.address = new Address(phone, myAddress);
    }
}
```

- API Member Controller

```java
package smallmall.smallmall.apiController;

import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import smallmall.smallmall.domain.Member;
import smallmall.smallmall.service.MemberService;

import javax.validation.Valid;

@RestController
@RequiredArgsConstructor
public class MemberController {

    private final MemberService service;

    @PostMapping("/members")
    public CreateMemberResponse saveMember (
            @RequestBody @Valid CreateMemberRequest request
    ){
        // member 객체를 생성하고 별도로 address 를 주입해줌
        Member member = new Member(request.getName());
        member.setAddress(request.getPhone(), request.getMyAddress());
        Long memberId = service.join(member);
        return new CreateMemberResponse(memberId);
    }

    @Data
    static class CreateMemberResponse{
        private Long id;
        public CreateMemberResponse(Long id) {
            this.id = id;
        }
    }
    @Data
    static class CreateMemberRequest{
        private String name;
        private String phone;
        private String myAddress;

    }
}
```

post man 의 Json 에 값을 각각 입력해주니 정상적으로 DB 에 등록이되었다.