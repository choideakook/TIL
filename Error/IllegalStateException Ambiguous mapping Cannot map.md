# IllegalStateException: Ambiguous mapping. Cannot map …

매핑이 에메모호해 발생한 에러다.

<br>

## ✏️ 발단

Controller 연습을 하던도중 서버를 실행시키니 해당 에러가 발생하면서 컴파일이 바로 종료되어버리는 문제가 발생했다.

- root couse

```java
Caused by: java.lang.IllegalStateException: Ambiguous mapping. Cannot map 'requestParamController' method
hello.springmvc.basic.request.RequestParamController#requestParamV4(String, int)
```

<br>

## ✏️ 원인

친절한 Spring 은 문제의 원인과 함께 문제가 발생한 Class 와 method 까지 알려준다.

매핑이 에메모호하다는 걸 보니 url 이 문제였다.

다른 method 를 복사 붙여넣기를 하다보니 url 을 미처 변경하지 못했다.

<br>

## ✏️ 해결

url 을 수정하니 정상적으로 서버가 작동했다.