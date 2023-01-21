# InstantiationException

```java
InstantiationException:
    No default constructor for entity
```

## ✏️ 발단

api 연습을 하던중 DB 수정에서 해당 에러가 발생했다.

해석해보면 entity 의 기본 생성자가 없다는 의미인것같다.

## ✏️ 문제 해결

해당 entity 로 가서 @NoArgsConstructor 를 입력하니 정상적으로 수정 api 가 작동했다.

혹시 몰라 옵션에 Protected 를 넣어 다른곳에서 기본 생성자를 사용 못하게 막아두었다.