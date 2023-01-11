# antlr.NoViableAltException: unexpected token

****No Viable / Alt / Exception****

불가능한 / 대안 / 예외

직역하면 무슨뜻인지 모르겠지만 보통 스펠링이 틀렸을 때 나타나는 예외이다.

<br>

## ✏️ 발단

스펠링은 intellij 에서 자동으로 완성해주고 틀릴경우엔 맞춤법 검사까지 해주기 때문에 왜 이 에러가 발생했는지 이해가 안됬다..

꼼꼼하게 확인해보다가 오타를 발견했다.

```java
public List<Member> findName(String name) {
        return em.createQuery("select m from Member m where Member.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();
    }
```

repositroy 의 create query 의 jpql 문에 오타가 있었다..

jpql 에서도 스펠링이 틀리면 intellij 가 잡아주지만,

로직이 틀렸을경우 자동완성이 안되기때문에 확인을 해주지 않는다.

<br>

## ✏️ 해결

where Member.name → m.name 으로 고치니 해결되었다.

```java
public List<Member> findName(String name) {
        return em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();
    }
```

intellij 덕분에 스펠링 틀릴 일이 거의 없으니 앞으로 이 오류가 발생하면 JPQL 문부터 확인해봐야겠다.