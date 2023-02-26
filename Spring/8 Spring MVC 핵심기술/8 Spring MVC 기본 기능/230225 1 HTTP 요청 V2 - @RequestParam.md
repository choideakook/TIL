# HTTP 요청 V2 - @RequestParam

Spring 이 제공하는 `@RequestParam` 을 사용하면 요청 파라미터를 매우 편리하게 사용할 수 있다.

## ✏️ @RequestParam

- @ResponseBody
    - `@Controller` 어노테이션이 있을경우 return 값을 String 으로 반환하면 View Path 로 인식하게 된다.
    - `@ResponseBody`어노테이션이 method 에 있을경우 `@Controller` 가 있어도 `@RestController` 처럼 String 값 그대로 data 를 응답하게 된다.
- @RequestParam
    - request.getParam() 과 동일한 기능이다.

```java
    @ResponseBody
    @RequestMapping("/request-param-v2")x
    public String requestParamV2(
            @RequestParam String username,
            @RequestParam("age") int userAge // 변수명이 변경될경우 명시해주어야 한다.
    ){
        log.info("username = {}, age = {}", username, userAge);
        return "ok";
    }
```

<br>

### 📍 @RequestParam 생략

- @RequestParam 는 사실 생략할 수 있다.
    - 단 요청 param 의 이름과 변수명이 동일해야 한다.
    - 매우 깔끔하고 간결한 코드가 완성되었다.

```java
    @ResponseBody
    @RequestMapping("/request-param-v4")
    public String requestParamV4(String username, int age) {

        log.info("username = {}, age = {}", username, age);
        return "ok";
    }
```

<br>

### ⚠️ String, int, Integer 등 단순 타입이라면 `@RequestParam` 도 생략 가능하다.

- 어노테이션을 생략해 간결하게 만드는 것도 좋지만,
`@RequestParam` 이 있으면 명확하게 요청 Parameter 에서 데이터를 읽는다는 것을 알 수 있다.

<br>

## ✏️ Parameter 필수 여부 - requestParamRequired

- `@RequestParam` 의 `required` 의 설정에 따라서 parameter 를 받지 않을 수 있다.
    - `required = true` 로 설정하면 요청 url 에 해당 parameter 가 없어도 에러가 발생하지 않는다.
    - default 값은 true 로 url 에 param 이 없을경우 발생하는 에러가 이 설정 때문이다.
        - 이 경우 400 bad request 코드가 응답된다.

```java
@ResponseBody
    @RequestMapping("/request-param-required")
    public String requestParamRequired(
            @RequestParam(required = true) String username, 
            @RequestParam(required = false) int age) {
        log.info("username = {}, age = {}", username, age);
        return "ok";
    }
```

<br>

### ⚠️ 분명 값을 false 로 했는데 500 error 가 발생한 경우

- @RequestParam 에 요청 값이 없다면 변수에 null 이 입력된다.
    - 만약 data type 이 int 라면 int 엔 null 을 넣을 수 없기 때문에 에러가 발생하게된다.
    - 만약 꼭 정수를 사용해야하는 경우라면 int 대신 Integer 로 바꿔주면 해결할 수 있다.
        - Integer 는 Wrapper Class 이기 때문에 null 을 포함할 수 있다.
        - int 는 자료형 이기 때문에 null 을 포함할 수 없다.

[🔗 int VS Integer](https://velog.io/@hadoyaji/int와-Integer는-무엇이-다른가)

<br>

## ✏️ Default Parameter - Default Value

- 앞의 상황처럼 요청 url 에 param 값이 없는경우 error 를 피하기 위해 서버에서 기본값을 변수에 담을 수 있다.
    - defaultValue 설정을 했을 때
    요청 url 에 param 값이 없다면 개발자가 설정한 기본값으로 변수값이 결정되게 된다.

```java
    @ResponseBody
    @RequestMapping("/request-param-default")
    public String requestParamDefault(
            @RequestParam(defaultValue = "guest") String username,
            @RequestParam(defaultValue = "-1") int age) {
        log.info("username = {}, age = {}", username, age);
        return "ok";
    }
```

🔍 log 출력값으로 확인

```java
h.s.b.r.RequestParamController : username = guest, age = -1
```

<br>

### ⚠️ Default Value 설정을 하면 요청 url 에 빈문자를 입력해도 기본 설정값이 입력된다.

- String 의경우 url 에 = 다음으로 아무 값도 적지않으면 “”; 값이 입력되지만,
Default Value 를 설정할 경우 “”; 이 아닌 설정값으로 변수값이 결정된다.

<br>

## ✏️ 요청 Parameter Map 으로 조회하기

- 요청 param 을 하나하나 매핑 해주지 않고 map 으로 한번에 매핑할 수도 있다.
    - map 뿐 아니라 multiValueMap 으로도 조회 가능하다.

```java
@ResponseBody
    @RequestMapping("/request-param-map")
    public String requestParamMap(
        @RequestParam Map<String, Objects> paramMap
    ) {
        log.info("username = {}, age = {}", paramMap.get("username"), paramMap.get("age"));
        return "ok";
    }
```