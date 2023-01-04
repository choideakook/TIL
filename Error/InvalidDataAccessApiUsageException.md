# InvalidDataAccessApiUsageException

## ✏️ 문제의 코드

```java
package jpabook.jpashop;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
class MemberRepositoryTest {

    @Autowired MemberRepository memberRepository;

    @Test
    void 회원정보저장 (){
        Member member = new Member();
        member.setId(1L);
        member.setUsername("memberA");

        Long save = memberRepository.save(member);
        Member memberId = memberRepository.find(member.getId());

        assertThat(memberId.getId()).isEqualTo(member.getId());
        assertThat(memberId.getUsername()).isEqualTo(member.getUsername());
    }
}
```

실행해보니 이러한 에러가 발생함

```yaml
InvalidDataAccessApiUsageException:
	 No EntityManager with actual transaction available for current thread 
		- cannot reliably process 'persist' call
```

Test 실행시 transaction 에노테이션이 없어서 발생한 오류

❗️ EntityManager 를 통한 모든 data 변경은 transaction 안에서 이루어저야함

참고 : [@Transaction](https://github.com/choideakook/TIL/blob/main/Spring/1%20Spring%20입문/4%20Spring%20DB%20접근%20기술/221214_스프링_통합_테스트.md)