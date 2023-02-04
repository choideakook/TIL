# My Batis

[🔗 My Batis 공식 메뉴얼](https://mybatis.org/mybatis-3/ko/index.html)

My Batis 는 JDBC Template 보다 더 많은 기능을 제공하는 SQL Mapper 이다.

JDBC Template 이 제공하는 대부분의 기능을 제공하고,
SQL 을 XML 에 편리하게 작성하며 동적 쿼리를 매우 편리하게 작성할 수 있는 장점이 있다.

- XML 에 작성한다는 점을 제외하면 JDBC 반복문을 줄여준다는 점에서 Template 과 거의 유사하다.
- **My Batis 의 단점**
    - JDBC Template 은 Spring 에 내장된 기능이고, 별도의 설정없이 사용할 수 있지만, 
    My Batis 는 약간의 설정이 필요하다.
    - 프로젝트에 복잡한 쿼리가 많다면 My Batis,
    단순한 쿼리들이 많다면 JDBC Template 을 선택하는것이 좋다.

<br>

## ✏️ My Batis 설정

### 📍 Dependencies 추가

My Batis 는 버전정보가 들어간다

- Spring 이 공식적으로 지원하는 Dependencies Spring 이 버전관리를 해주기 때문에 별도로 버전을 명시하지 않으면 최적화를 해준다.
- My Batis 는 Spring 이 공식적으로 지원하지 않기 때문에 버전정보를 명시해주어야 한다.

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//JdbcTemplate 추가
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
//MyBatis 추가
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0'
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

### 📍application.yml 환경설정

지금은 main 과 test 를 각각 운영하고 있기 때문에 설정도 각각 해주어야 한다.

```yaml
# DB 환경설정
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
  # domain 의 필드를 기준으로 자동으로 DB 응답 값을 받아줌
  # 만약 이 설정을 하지 않으면 응답값이 필요한 로직에 디렉토리를 일일이 적어주어야 한다.
  # 패키지명 부터 시작해서 필드를 코드에 작성해야한다.
  # 지정한 위치를 포함해 하위 패키지가 자동으로 인식되고 , 이나 ; 로 여러 패키지를 등록할 수 있다.
  type-aliases-package: hello.itemservice.domain
  # db 의 언더스코어 문법을 java 의 낙타 문법으로 번역해줌 (기본값이 false 이다.)
  configuration:
    map-underscore-to-camel-case: true

# 로그
logging:
  level:
    # Jdbc Template sql log
    org:
      hibernate:
        SQL: debug
    # my batis sql log
    hello:
      itemservice:
        repository:
          mybatis: trace
```