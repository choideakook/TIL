# Dependency Injection 주입 방식

## ✏️ DI (Dependency Injection) 의 3가지 방식

1. 생성자 주입 방식 (가장 많이 사용되는 방식)
2. 필드 주입 방식
3. setter 주입방식

### 1. 생성자 주입 방식

주입하고 싶은 Class 를  변수로 선언한 후

생성자를 생성해 해당 Class 로 가져오는 방식

```java
private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
```

💡 생성자가 public 으로 공개되어있긴 하지만 변수에 final 을 붙여서 중간이 변경될 수 있는 문제를 예방해준다.

### 2. 필드 주입 방식

생성자 없이 변수에 바로 어노테이션을 붙여줌

(final 은 빼줘야함)

```java
@Autowired private MemberService memberService;
```

⚠️ 필드 주입은 오류가 자주나는 방식이고 세세한 조정이 어려워 자주 사용하지 않는다.

### 3. Setter 주입 방식

생성자 대신 setter 매소드를 생성한 후 주입시켜줌

(변수의 final 은 빼줘야함)

```java
private MemberService memberService;

    @Autowired
    public  void setMemberService (MemberService memberService){
        this.memberService = memberService;
    }
```

⚠️  매소드가 public 으로 열려있어서 해당 매소드가 호출되거나 변경 되어서는 안된다.

따라서 중간에 바뀔 수 있는 위험이 있기때문에 잘 사용하지 않는다.
