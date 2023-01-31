# Transaction Template

## ✏️ Template Call back pattern

### 📍 필요성

Transaction Manager 를 사용하면 Service 계층에서 Transaction 을 시작하고,
try , catch 문을 반복적으로 사용해야한다.

business 로직이 try 안에 위치하기 때문에 Method 로 리팩토링 하는것도 까다롭게 된다.

<br>

### 📍Template Call back pattern 으로 문제 해결

Transaction Manager 를 주입받으면서 Transaction Template 을 사용한다.

- 생성자에서 Manager 를 주입받음

```java
package hello.jdbc.service;

import hello.jdbc.domain.Member;
import hello.jdbc.repository.MemberRepositoryV3;
import lombok.extern.slf4j.Slf4j;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.support.TransactionTemplate;

import java.sql.SQLException;

/**
 * Transaction - Transaction Template
 */
@Slf4j
public class MemberServiceV3_2 {

    // private final PlatformTransactionManager transactionManager;

    private final TransactionTemplate txTemplate;
    private final MemberRepositoryV3 memberRepository;

    public MemberServiceV3_2(PlatformTransactionManager transactionManager, MemberRepositoryV3 memberRepository) {
        // txTemplate 에 transactionManager 를 param 으로 하는 new TransactionTemplate 를 주입
        this.txTemplate = new TransactionTemplate(transactionManager);
        this.memberRepository = memberRepository;
    }

    // Transaction Logic
    public void accountTransaction(String fromId, String toId, int money) throws SQLException {

        // TransactionTemplate 을 호출하면 모든 로직이 실행되고 종료된다.
        txTemplate.executeWithoutResult(status ->{
            // Business logic
            try {
                buzLogic(fromId, toId, money);
            } catch (SQLException e) {
                throw new IllegalStateException(e);
            }
        });
    }

    // Business logic
    private void buzLogic(String fromId, String toId, int money) throws SQLException {
        Member fromMember = memberRepository.findById(fromId);
        Member toMember = memberRepository.findById(toId);

        int fromMemberMoney = fromMember.getMoney();
        int toMemberMoney = toMember.getMoney();

        if (fromMemberMoney - money < 0) throw new IllegalStateException("잔액이 부족합니다.");

        memberRepository.update(fromId, fromMemberMoney - money);

        if (toId.equals("ex")) throw new IllegalStateException("이체중 예외 발생");

        memberRepository.update(toId, toMemberMoney + money);
    }
}
```

<br>

### 📍 Test

기존 Test 의 Service V3_1 을 V3_2 로 바꾸면 Template 로 변경이 완료된다.

- 작동이 정상적으로 실행된다.

🔗 Test code 보기

<br>

### 📍 Transaction Template 의 문제점

- 코드 자체는 줄었지만 business 로직을 만들어야하는 곳에 Transaction 을 처리하는 기술 로직이 포함되어있다.
    - 만약 향후에 Transaction 기술을 사용하지 안게되면 Service 로직을 다시 수정해야한다.
- business 로직과 Transaction **************로직이 한곳에 있으면 두 관심사를 하나의 Class 에서 처리하게된다.**************
    - 결과적으로 유지보수가 어려워짐
- Service 계층의 순수성이 지켜지지가 않는다.