# Intellij 프로젝트 생성후 세팅

## ✏️ Project 생성
[🔗 Project 생성 링크](https://start.spring.io)  
  
### 📍 Dependencies
- Gradle Groovy
- Spring Boot 2.7.7
- Java 11
- Dependencies
    - Spring web
    - Validation [🔗 Validation 을 추가해야하는 이유](https://github.com/choideakook/TIL/blob/main/Error/Cannot%20resolve%20symbol%20'Valid’.md)
    - Thymeleaf
    - jpa
    - H2 Database
    - Lombok
    - Spring boot devtools
- Group : 기관 명
- Artifact : project 명 (build 결과물)
  

## ✏️ Gradle 변경

Intellij 로 변경

<img width="500" alt="1" src="https://user-images.githubusercontent.com/115536240/211440307-55c3dec8-3fd5-45ff-b0e2-fa0e2cd3edd6.png">

## ✏️ Lombok 세팅

Preference → plugin → lombok 최신버전인지 확인

<img width="500" alt="2" src="https://user-images.githubusercontent.com/115536240/211440310-90dd8f58-4629-4ac5-b3ed-3ffaa43f7bfc.png">

enable annotation processing 에 체크
  - 롬복 같은 외부 라이브러리가 컴파일 시 문제없이 작동하도록 해주는 설정

build.gradel 의 dependencies 추가
  - 테스트에서 롬복을 사용할 수 있게된다.
  - dependencies 에 추가하면 됨
```
//테스트에서 lombok 사용
    testCompileOnly 'org.projectlombok:lombok'
    testAnnotationProcessor 'org.projectlombok:lombok'
```

## ✏️ git 자동 staging

git → Enable staging area 에 체크

<img width="500" alt="3" src="https://user-images.githubusercontent.com/115536240/211440314-25ab601c-53de-49de-9386-3e9878c3ba8b.png">

confirmation → when files are created → Add silently ( 자동으로 add 하기) 에 체크

- 앞으로 생성되는 file 들은 자동으로 add 됨

터미널에서 git repository 에 push 해주면 기존 파일들이 repository 에 업로드되고 앞으로의 파일들도 자동 업로드 된다.
  
## ✏️ application 실행해보기
SpringBootApplication 을 실행 후  
```
Started JdbcApplication 메시지가 나오면 성공  
```  
  
<img width="600" alt="s000" src="https://user-images.githubusercontent.com/115536240/214991902-844da971-78be-4366-b601-8c3acff8066f.png">  
  
## ✏️ H2 DB 와 JPA 환경설정
application.yml 에 환경설정 하기  

[🔗 환경설정의 자세한 내용](https://github.com/choideakook/TIL/blob/main/Spring/3%20JPA%20활용1/1%20프로젝트%20환경설정/230104%201%20프로젝트%20환경설정.md)  
[🔗 Spring Boot 공식 사용 설명서](https://spring.io/projects/spring-boot#learn)  
[🔗 Logging level 설정에 관한 설명서](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20핵심기술/8%20Spring%20MVC%20기본%20기능/230216%201%20Logging.md)

```yaml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/h2이름
    username: sa
    password:
    driver-class-name: org.h2.Driver

  jpa:
    hibernate:
      ddl-auto: create
    properties:
      hibernate:
        format_sql: true

logging.level:
  org.hibernate:
    SQL: debug
#    type: trace (Spring boot 2.X.X)
#    orm.jdbc.bind: trace (Spring boot 3.X.X)
```  
  
### 📍Test 용 application.yml
```yaml
spring:

logging.level:
  org.hibernate.SQL: debug
  org.hibernate.orm.jdbc.bind: trace
```
~ 로 디렉토리 이동후 test.mv.db 파일 카피  
  - cp 대상파일명 복사파일명  

디렉토리 H2 의 bin 으로 이동  
- 권한 부여
```
chmod 755 h2.sh
```
- h2 실행
```
./h2.sh
```
```
localhost
```
  
JDBC URL 접속
```
jdbc:h2:tcp://localhost/~/DB 이름
```
  
새로운 DB 를 만드려면
```
jdbc:h2:~/DB 이름
```
~ 에 새로운 mv.db 가 생성된걸 확인할 수 있다.  
  
## ✏️ QueryDSL 설정
  
```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
//JPA, 스프링 데이터 JPA 추가
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
//Querydsl 추가
	implementation 'com.querydsl:querydsl-jpa'
	annotationProcessor "com.querydsl:querydsl-apt:${dependencyManagement.importedProperties['querydsl.version']}:jpa"
	annotationProcessor "jakarta.annotation:jakarta.annotation-api"
	annotationProcessor "jakarta.persistence:jakarta.persistence-api"
//H2 데이터베이스 추가
	runtimeOnly 'com.h2database:h2'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
//테스트에서 lombok 사용
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
}

tasks.named('test') {
	useJUnitPlatform()
}

//Querydsl 추가, 자동 생성된 Q클래스 gradle clean으로 제거
clean {
	delete file('src/main/generated')
}
```
  
### 📍 Q Type 생성
[🔗 Q Type](https://github.com/choideakook/TIL/tree/main/Spring/7%20DB%20접근%20활용/6%20QueryDSL)  
- Entity 를 생성하면 필드를 기반으로 Q Type 이 생성된다.
  - application 실행해서 생성하기
  - build 의 Rebuild (S + C + F9) 로 생성하기
  
❗️ generated 는 git ignore 로 repository 에 업로드가 안되게 막아주어야 한다.  
  
<br>

## ✏️ AssertJ import

- dependencies 에 추가해주면 된다.

```
testImplementation "org.assertj:assertj-core:3.20.2"
```
