# JDBC Template

## ✏️ JDBC Template

JDBC Template 은 spring-jdbc 라이브러리에 포함되어 있어
spring 으로 jdbc 를 사용할 때 기본적으로 사용되는 라이브러리이다.

### 📍 장점

- 설정의 편리함
    - 별도의 복잡한 설정 없이 바로 사용할 수 있다는 장점이 있다.
- 반복문제 해결
    - 커넥션 획득
    - statement 를 준비해고 실행
    - 결과를 반복하도록 루프를 실행
    - connection, statement, resultset close
    - 트랜젝션을 다루기 위한 커넥션 동기화
    - 예외 발생시 Spring 예외 변환기 실행

### 📍 단점

- 동적 SQL 을 해결하기 어렵다.

<br>

## ✏️ JDBC 라이브러리 사용하기

project 를 시작하기 전이라면 jdbc 를 추가해 프로젝트를 생성하면 되고,

- spring data JDBC

프로젝트가 이미 생성되었다면 dependency 에서 별도로 추가해주면된다.

```java
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    //JdbcTemplate 추가
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    //H2 데이터베이스 추가
    runtimeOnly 'com.h2database:h2'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    //테스트에서 lombok 사용
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
}
```

<br>

## ✏️ JDBC Template 의 CRUD

- template.update
    - DB 등록, 수정, 삭제
    - insert, update, delete
    - update 의 반환값은 int 인데 update 로 인해 영향을 받은 row 숫자를 뜻한다.
    - insert PK 로직
        - [🔗 개발자가 직접 PK 설정 - KeyHolder](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/1%20JDBC%20Template/230202%202JDBC%20Template%20적용.md)
        - [🔗 자동으로 PK 설정 - SimpleJdbcInsert](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/1%20JDBC%20Template/230202%204%20Simple%20JDBC%20Insert.md)
- template.query
    - List 를 조회
    - DB 의 2개 이상의 data 를 조회할경우 사용한다.
    - select
    - row mapper 를 통해 DB 의 응답값 result set 을 받아와야 한다.
- template.queryForObject
    - DB 의 특정 data 를 조회
    - 단건 조회에 사용된다.
    - select
    - row mapper 를 통해 DB 의 응답값 result set 을 받아와야 한다.
