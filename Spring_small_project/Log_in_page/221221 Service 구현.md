# 221221 Service 구현

```java
package login.loginspring.service;

import login.loginspring.domain.Member;
import login.loginspring.repository.MemberRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

public class MemberService {

    private final MemberRepository repository;

    @Autowired
    public MemberService(MemberRepository repository) {
        this.repository = repository;
    }

    public Long join(Member member) {
        duplicater(member);

        repository.save(member);
        return member.getId();
    }

    private void duplicater(Member member) {
        repository.findByUserId(member.getUserid())
                .ifPresent(member1 -> {
                    throw new IllegalStateException(
                            "이미 존재하는 id 입니다."
                    );
                });
    }

    public List<Member> findMembers(){
        return repository.findAll();
    }

    public Optional<Member> findOne (Long id){
        return repository.findById(id);
    }

}
```