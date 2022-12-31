# Optional<>

## ✏️ Optional 의 특징

- T 타입 객체의 래퍼클래스
    - 래퍼 클래스 : Integer , Long … 제네릭을 이용해야 하는 Class
    - T 타입 : 모든 종류의 객체를 뜻함
- obtional 은 모든 종류의 객체를 다룰 수 있는 Class임
    - null 도 다룰 수 있기 때문에 간접적으로 null 을 다뤄야 할때도 사용함

## ⚠️ null 을 다뤄야 할 때 Optional 이 효과 적인 이유

```java
Object result = getResult();
```

이 경우 값이 객체 또는 null 두가지 중 한가지가 들어갈 수 있다.

만약 getResult() 가 null 일경우 null pointer exception 에러가 발생할 수 있다.

따라서 null 이 발생할 위험이 있을경우 if 문이 자동으로 따라와줘야 하는데

이렇게 되면 코드가 지저분해지기때문에 Optional 이 더 적합하다.

### 📍 Optional 이 null 을 다루는 원리

Optional 은 null 도 객체처럼 다룰 수 있기 때문에 getResult() 의 값이 null 일경우

Optional 의 객체에 null 을 담을 수 있음

따라서 result 의 값이 객체가 되므로 null pointer exception 이 발생하지 않음

## ⚙️ Method

### 📍 of()

Optional 객체를 생성하는 가장 기본적인 방법이지만 이 method 로 null 을 넣을 수는 없다.

```java
String str = "Hello World";

Optional<String> optional = Optional.of(str);
```

- 만약 괄호안에 null 을 넣을 경우 null pointer exception 발생

### 📍 ofNullable()

null 객체를 생성할 수 있는 기능

```java
Optional<String> optional = Optional.ofNullable(null);
```

### 📍empty()

Optional 객체를 초기화 하는 기능

```java
String str = "Hello World";

Optional<String> optional = Optional.ofNullable(str);
optional = Optional.empty();
```

💡 반복문 사용할 때

String = “”; , int = 0;

이런식으로 초기화 하는것과 같은 개념

---

### 📍 get()

Optional 객체를 return 하는 기능

```java
String str = "Hello World";

Optional<String> optional = Optional.ofNullable(str);

System.out.println(optional.get());  // Hello World
```

- null 은 return 할 수 없다.

### 📍 orElse()

null 을 포함한 모든 객체를 return 하는 기능

❗️값이 null 일경우 괄호 안의 값을 대신 return 한다.

```java
String str = "Hello World";

Optional<String> optional = Optional.ofNullable(str);

System.out.println(optional.orElse("hi")); // Hello World

optional = Optional.ofNullable(null);

System.out.println(optional.orElse("hi"));  // hi
```

---

### 📍 isPresent

객체가 null 일경우 false / null 이 아닐경우 true 를 반환하는 기능

```java
// id 중복 체크 기능
Optional <Member> result 
				= memberRepository.findByName(member.getName())

if (result.isPresent())
				System.out.println("이미 사용중인 id");
else
				System.out.println("사용 가능한 id");
```

### 📍 ifPresent

객체가 null 일경우 pass / null 이 아닐경우 실행 하는 기능

```java
// id 중복 체크 기능
memberRepository.findByName(member.getName())
     .ifPresent(m -> {
         throw new IllegalStateException("이미 존재하는 회원입니다.");
     });
```