# HTTP 요청 - Parameter (@ModelAttribute)

## ✏️ @ModelAttribute

- @RequestParam 을 사용하지 않고 별도의 Data 전달용 객체를 생성해 매핑하는 방식

### 📍 전송용 객체 만들기

- @ModelAttribute 를 사용하기 위해선 먼저 Data 전송용 객체를 생성해야 한다.
    - @Data 를 만들어 getter / setter / toString 기능을 사용할 수 있게 해준다.

```java
package hello.springmvc2.basic;

import lombok.Data;

@Data
public class HelloData {

    private String username;
    private int age;
}
```

<br>

### 📍 매핑 method 만들기

- 매개변수 값에 방금 만든 Data 전송용 객체를 어노테이션과 함께 넣어준다.
    - 이렇게 생성된 변수의 필드에 요청 url 의 parameter 가 저장되고,
    get 과 toString 으로 편하게 사용할 수 있게 된다.

```java
    @ResponseBody
    @RequestMapping("/model-attribute-v1")
    public String modelAttributeV1(@ModelAttribute HelloData helloData) {

        log.info("username={}, age={}", helloData.getUsername(),helloData.getAge());
        return "ok";
    }
```

- ***@ModelAttribute 의 원리***
    - @ModelAttribute 는 입력된 객체를 프로젝트 내에서 찾아낸다.
    - 찾아낸 객체의 필드에 setter 를 통해 url 의 param 값을 입력해 준다.
    - method 에서 url 의 param 을 getter 를 사용해 편리하게 사용할 수 있게 된다.
- ***@ModelAttribute 생략***
    - @RequestParam 처럼 @ModelAttribute 도 생략이 가능하다.
    - int 나 Sting 같은 자료형이 아닌 참조형 객체일경우 자동으로 @ModelAttribute 로 인식된다.

```java
    @ResponseBody
    @RequestMapping("/model-attribute-v2")
    public String modelAttributeV1(HelloData helloData) {

        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
```

⚠️ 주의점

- 만약 url 의 param 명과 객체의 필드명이 다를경우 매핑되지 않는다.
- data type 이 맞지 않을경우 400 에러 (binding exception 이 발생된다.
    - ex) url 에선 문자를 입력했지만 필드 타입이 int 경우