# Cannot resolve symbol 'Valid’

## ✏️ 발단

Controller 에서 API 를 이용해 정보를 저장하는 로직을 만들던중

@Valid 가 import 되지 않는 문제를 겪었다.

<br>

## ✏️ 원인

Spring boot 2.3 버전까지는 Web dependency 를 추가하면 자동으로 v****javax.validation 까지 추가가 됬지만,****

이후 버전 부터는 ****javax.validation 를**** 가져오지 않기 때문에 별도로 추가를 해줘야 한다.

<br>

## ✏️ 문제 해결

1. project 생성시 Validation dependency 를 추가한다.
2. 1번을 못한 상태로 project 를 진행할 경우 별도로 추가를 해야한다.
    - build.gradle 에서 dependencies 에 추가해주면됨

```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

- 정상적으로 import 가 된다.
