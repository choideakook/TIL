# My Batis

[๐ย My Batis ๊ณต์ ๋ฉ๋ด์ผ](https://mybatis.org/mybatis-3/ko/index.html)

My Batis ๋ JDBC Template ๋ณด๋ค ๋ ๋ง์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ SQL Mapper ์ด๋ค.

JDBC Template ์ด ์ ๊ณตํ๋ ๋๋ถ๋ถ์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๊ณ ,
SQL ์ XML ์ ํธ๋ฆฌํ๊ฒ ์์ฑํ๋ฉฐ ๋์  ์ฟผ๋ฆฌ๋ฅผ ๋งค์ฐ ํธ๋ฆฌํ๊ฒ ์์ฑํ  ์ ์๋ ์ฅ์ ์ด ์๋ค.

- XML ์ ์์ฑํ๋ค๋ ์ ์ ์ ์ธํ๋ฉด JDBC ๋ฐ๋ณต๋ฌธ์ ์ค์ฌ์ค๋ค๋ ์ ์์ Template ๊ณผ ๊ฑฐ์ ์ ์ฌํ๋ค.
- **My Batis ์ ๋จ์ **
    - JDBC Template ์ Spring ์ ๋ด์ฅ๋ ๊ธฐ๋ฅ์ด๊ณ , ๋ณ๋์ ์ค์ ์์ด ์ฌ์ฉํ  ์ ์์ง๋ง, 
    My Batis ๋ ์ฝ๊ฐ์ ์ค์ ์ด ํ์ํ๋ค.
    - ํ๋ก์ ํธ์ ๋ณต์กํ ์ฟผ๋ฆฌ๊ฐ ๋ง๋ค๋ฉด My Batis,
    ๋จ์ํ ์ฟผ๋ฆฌ๋ค์ด ๋ง๋ค๋ฉด JDBC Template ์ ์ ํํ๋๊ฒ์ด ์ข๋ค.

<br>

## โ๏ธย My Batis ์ค์ 

### ๐ย Dependencies ์ถ๊ฐ

My Batis ๋ ๋ฒ์ ์ ๋ณด๊ฐ ๋ค์ด๊ฐ๋ค

- Spring ์ด ๊ณต์์ ์ผ๋ก ์ง์ํ๋ Dependencies Spring ์ด ๋ฒ์ ๊ด๋ฆฌ๋ฅผ ํด์ฃผ๊ธฐ ๋๋ฌธ์ ๋ณ๋๋ก ๋ฒ์ ์ ๋ช์ํ์ง ์์ผ๋ฉด ์ต์ ํ๋ฅผ ํด์ค๋ค.
- My Batis ๋ Spring ์ด ๊ณต์์ ์ผ๋ก ์ง์ํ์ง ์๊ธฐ ๋๋ฌธ์ ๋ฒ์ ์ ๋ณด๋ฅผ ๋ช์ํด์ฃผ์ด์ผ ํ๋ค.

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//JdbcTemplate ์ถ๊ฐ
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
//MyBatis ์ถ๊ฐ
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0'
//H2 ๋ฐ์ดํฐ๋ฒ ์ด์ค ์ถ๊ฐ
	runtimeOnly 'com.h2database:h2'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
//ํ์คํธ์์ lombok ์ฌ์ฉ
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
}
```

### ๐application.yml ํ๊ฒฝ์ค์ 

์ง๊ธ์ main ๊ณผ test ๋ฅผ ๊ฐ๊ฐ ์ด์ํ๊ณ  ์๊ธฐ ๋๋ฌธ์ ์ค์ ๋ ๊ฐ๊ฐ ํด์ฃผ์ด์ผ ํ๋ค.

```yaml
# DB ํ๊ฒฝ์ค์ 
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
  # domain ์ ํ๋๋ฅผ ๊ธฐ์ค์ผ๋ก ์๋์ผ๋ก DB ์๋ต ๊ฐ์ ๋ฐ์์ค
  # ๋ง์ฝ ์ด ์ค์ ์ ํ์ง ์์ผ๋ฉด ์๋ต๊ฐ์ด ํ์ํ ๋ก์ง์ ๋๋ ํ ๋ฆฌ๋ฅผ ์ผ์ผ์ด ์ ์ด์ฃผ์ด์ผ ํ๋ค.
  # ํจํค์ง๋ช ๋ถํฐ ์์ํด์ ํ๋๋ฅผ ์ฝ๋์ ์์ฑํด์ผํ๋ค.
  # ์ง์ ํ ์์น๋ฅผ ํฌํจํด ํ์ ํจํค์ง๊ฐ ์๋์ผ๋ก ์ธ์๋๊ณ  , ์ด๋ ; ๋ก ์ฌ๋ฌ ํจํค์ง๋ฅผ ๋ฑ๋กํ  ์ ์๋ค.
  type-aliases-package: hello.itemservice.domain
  # db ์ ์ธ๋์ค์ฝ์ด ๋ฌธ๋ฒ์ java ์ ๋ํ ๋ฌธ๋ฒ์ผ๋ก ๋ฒ์ญํด์ค (๊ธฐ๋ณธ๊ฐ์ด false ์ด๋ค.)
  configuration:
    map-underscore-to-camel-case: true

# ๋ก๊ทธ
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