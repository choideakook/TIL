# Transaction Template

## โ๏ธย Template Call back pattern

### ๐ย ํ์์ฑ

Transaction Manager ๋ฅผ ์ฌ์ฉํ๋ฉด Service ๊ณ์ธต์์ Transaction ์ ์์ํ๊ณ ,
try , catch ๋ฌธ์ ๋ฐ๋ณต์ ์ผ๋ก ์ฌ์ฉํด์ผํ๋ค.

business ๋ก์ง์ด try ์์ ์์นํ๊ธฐ ๋๋ฌธ์ Method ๋ก ๋ฆฌํฉํ ๋ง ํ๋๊ฒ๋ ๊น๋ค๋กญ๊ฒ ๋๋ค.

<br>

### ๐Template Call back pattern ์ผ๋ก ๋ฌธ์  ํด๊ฒฐ

Transaction Manager ๋ฅผ ์ฃผ์๋ฐ์ผ๋ฉด์ Transaction Template ์ ์ฌ์ฉํ๋ค.

- ์์ฑ์์์ Manager ๋ฅผ ์ฃผ์๋ฐ์

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
        // txTemplate ์ transactionManager ๋ฅผ param ์ผ๋ก ํ๋ new TransactionTemplate ๋ฅผ ์ฃผ์
        this.txTemplate = new TransactionTemplate(transactionManager);
        this.memberRepository = memberRepository;
    }

    // Transaction Logic
    public void accountTransaction(String fromId, String toId, int money) throws SQLException {

        // TransactionTemplate ์ ํธ์ถํ๋ฉด ๋ชจ๋  ๋ก์ง์ด ์คํ๋๊ณ  ์ข๋ฃ๋๋ค.
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

        if (fromMemberMoney - money < 0) throw new IllegalStateException("์์ก์ด ๋ถ์กฑํฉ๋๋ค.");

        memberRepository.update(fromId, fromMemberMoney - money);

        if (toId.equals("ex")) throw new IllegalStateException("์ด์ฒด์ค ์์ธ ๋ฐ์");

        memberRepository.update(toId, toMemberMoney + money);
    }
}
```

<br>

### ๐ย Test

๊ธฐ์กด Test ์ Service V3_1 ์ V3_2 ๋ก ๋ฐ๊พธ๋ฉด Template ๋ก ๋ณ๊ฒฝ์ด ์๋ฃ๋๋ค.

- ์๋์ด ์ ์์ ์ผ๋ก ์คํ๋๋ค.

[๐ย Test code ๋ณด๊ธฐ](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20์ ๊ทผ%20ํต์ฌ%20์๋ฆฌ/4%20Transaction%20ํ์ฉ/230130%203%20Transaction%20Manager%20์ ์ฉ.md)

<br>

### ๐ย Transaction Template ์ ๋ฌธ์ ์ 

- ์ฝ๋ ์์ฒด๋ ์ค์์ง๋ง business ๋ก์ง์ ๋ง๋ค์ด์ผํ๋ ๊ณณ์ Transaction ์ ์ฒ๋ฆฌํ๋ ๊ธฐ์  ๋ก์ง์ด ํฌํจ๋์ด์๋ค.
    - ๋ง์ฝ ํฅํ์ Transaction ๊ธฐ์ ์ ์ฌ์ฉํ์ง ์๊ฒ๋๋ฉด Service ๋ก์ง์ ๋ค์ ์์ ํด์ผํ๋ค.
- business ๋ก์ง๊ณผ Transaction **************๋ก์ง์ด ํ๊ณณ์ ์์ผ๋ฉด ๋ ๊ด์ฌ์ฌ๋ฅผ ํ๋์ Class ์์ ์ฒ๋ฆฌํ๊ฒ๋๋ค.**************
    - ๊ฒฐ๊ณผ์ ์ผ๋ก ์ ์ง๋ณด์๊ฐ ์ด๋ ค์์ง
- Service ๊ณ์ธต์ ์์์ฑ์ด ์ง์ผ์ง์ง๊ฐ ์๋๋ค.
