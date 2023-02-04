# JPA 환경 설정

## ✏️ Dependencies 추가

- JPA 에 JDBC 도 포함되어있기 때문에 JDBC 는 dependency 에서 제거해도 된다.
- JPA 를 추가하면 Libraries 에 data-jpa , Hibernate 와 Persistence 가 추가된걸 확인할 수 있다.

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//JdbcTemplate
	//implementation 'org.springframework.boot:spring-boot-starter-jdbc'
//MyBatis
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0'
//JPA, 스프링 데이터 JPA 추가
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
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

## ✏️ application.yml 환경설정

[🔗 JPA 환경설정](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Intellij%20프로젝트%20생성후%20기본%20세팅.md)

- org.hibernate.SQL: DEBUG
    - hibernate 가 생성하고 실행하는 SQL 을 확인할 수 있다.
- type.descriptor.sql.BasicBinder: TRACE
    - SQL 에 바인딩 되는 Parameter 를 확인할 수 있다.

```yaml
spring:
  profiles:
    active: local
  datasource:
    url: jdbc:h2:tcp://localhost/~/itemservice-db
    username: sa
    password:
    driver-class-name: org.h2.Driver

# My Batis
mybatis:
  type-aliases-package: hello.itemservice.domain
  configuration:
    map-underscore-to-camel-case: true

# hibernate log
logging:
  level:
    org.hibernate.SQL: DEBUG
    type.descriptor.sql.BasicBinder: TRACE
```

<br>

⚠️ JAP 를 설정하려면 

Entity Manager Factory, JPA Transaction Manager, Data Source 등등

다양한 설정을 해야 하지만,
Spring Boot 는 이 모든 과정을 자동화 해준다.

- Spring Boot 의 자동 설정은 JpaBaseConfiguration 을 통해 자동으로 등록된다.