# HTTP Message Converter

## ✏️ HTTP Message Converter

### 📍 Converter 의 필요성

View 탬플릿으로 HTML 을 생성해 응답하는 것이 아닌,

HTTP API 또는 단순 Text 처럼 message body 를 직접 읽거나 쓰는 경우 

HTTP message Converter 를 사용하는 것이 좋다.

<br>

### 📍@ResponseBody 의 원리

[🔗 @ResponseBody](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230226%202%20HTTP%20요청%20-%20단순%20text.md)

[🔗 @View Resolver](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/5%20MVC%20프레임워크%20만들기/230215%202%20V3%20-%20Model%20추가.md)

1. 브라우저에서 api 요청이 들어옴
2. Spring Container 의 method 가 url 에 의해 호출됨
3. `@ResponseBody` 나 `@RestController` 가 선언되어 있다면 `HTTP Message Converter` 가 작동된다.
    - 기본 `@Controller` 만 선언되어 있다면 `View Resolver` 가 작동된다.
4. `HTTP Message Converter` 는 String 반환값을 JSON 또는 Text 로 변경해 응답한다.
    - HTTP body 에 내용이 직접 반환된다.
    - 기본 문자일경우 `StringHttpMessageConverter` 로 단순 Text 반환
    - 객체 일경우 `MappingJackson2HttpMessageConverter` 로 JSON 반환

<img width="551" alt="s882" src="https://user-images.githubusercontent.com/115536240/221516914-1f8f600b-5c16-4448-8da4-5fe4a1ea16eb.png">

<br>

### 📍 HTTP Message Converter 의 기본 개념

- HTTP 요청, 응답 둘 다 사용된다.
    - `canRead()` , `canWirte()` - 메시지 컨버터가 해당 Class, 미디어 타입을 지원하는지 체크
        - 미디어 타입은 header 의 Content-type 을 의미한다.
        - Content-type 을 확인해 컨버팅이 가능한지, 어떤 형태로 컨버팅을 해야 하는지 판단한다.
    - `read()` , `write()` - 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능

<br>

### 📍 알아두면 좋은 message converter

- `ByteArrayHttpMessageConverter` : `byte[]` 데이터를 처리한다.
    - 클래스 타입: `byte[]` , 미디어타입: */* (아무 타입이나 수용가능)
    - 요청 예) `@RequestBody byte[] data`
    - 응답 예) `@ResponseBody return byte[]`
        - 쓰기 미디어타입 `application/octet-stream`

<br>

- `StringHttpMessageConverter` : String 문자로 데이터를 처리한다.
    - 클래스 타입: String , 미디어타입: */* (아무 타입이나 수용가능)
    - 요청 예) `@RequestBody String data`
    - 응답 예) `@ResponseBody return "ok"`
        - 쓰기 미디어타입 `text/plain`

<br>

- `MappingJackson2HttpMessageConverter` : `application/json`
    - 클래스 타입: 객체 또는 `HashMap` , 미디어타입 `application/json` 관련
    - 요청 예) `@RequestBody HelloData data`
    - 응답 예) `@ResponseBody return helloData`
        - 쓰기 미디어타입 `application/json` 관련

<br>

### 📍 HTTP 요청 데이터 읽기 과정

1. HTTP 요청이 오고, Controller 에서 @RequestBody , HttpEntity 파라미터를 사용한다.
2. 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 `canRead()` 를 호출한다.
    - 대상 Class 타입을 지원하는지
    - HTTP 요청의 `Content-type 미디어 타입` 을 지원하는지
3. `canRea()` 조건을 만족하면 `read()` 를 호출해서 객체를 생성하고, 반환한다.

<br>

### 📍 HTTP 응답 데이터 생성 과정

1. Controller 에서 @ResponseBody, HttpEntity 로 값이 반환된다.
2. 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 `canWrite()` 를 호출한다.
    - 대상 Class 타입을 지원하는지
    - HTTP 요청의 `Accept 미디어 타입` 을 지원하는지
3. `canWrite()` 의 조건을 만족하면 `wirte()` 를 호출해 HTTP 응답 메시지를 body 에 생성한다.
