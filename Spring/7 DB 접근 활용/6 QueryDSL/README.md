# QueryDSl

Domain Specific Language (도메인 특화 언어)

- 특정 도메인에 초점을 맞춘 언어
- 즉 QueryDSl 은 Query 에 특화된 프로그래밍 언어라고 할 수 있다.

<br>

## ✏️ 환경설정

### 📍 Build.gradle

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//JdbcTemplate 추가
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
//MyBatis 추가
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0'
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

<br>

### 📍 Q Type 생성

- Entity 와 필드를 기반으로 Query DSL 을 사용할 수 있게 Q Type 에 자동으로 로직을 만들어준다.
- gadle 설정을 완료한 뒤 application 을 실행하면 generated 폴더에 Entity 와 동일한 경로로 Q Type Class 가 생성된다.
    - build 의 Rebuild (S + C + F9) 를 실행해도 class 가 생성됨
    - 참고로 generated 는 git ignore 로 repository 에 업로드가 안되게 막아주어야 한다.

<img width="350" alt="s7611" src="https://user-images.githubusercontent.com/115536240/216801838-8a3401b5-8fb8-4343-9e88-d57a7c6cbb1b.png">

### 📍 Q type 삭제

gradle 에서 설정했던 Clean 명령어를 실행하면 Q type 을 삭제해준다.

```java
//Querydsl 추가, 자동 생성된 Q클래스 gradle clean으로 제거
clean {
	delete file('src/main/generated')
}
```

더블클릭하면 작동한다.

<img width="350" alt="s7612" src="https://user-images.githubusercontent.com/115536240/216801841-67d1da88-ed64-4202-8d6d-c80ddcc1b200.png">
