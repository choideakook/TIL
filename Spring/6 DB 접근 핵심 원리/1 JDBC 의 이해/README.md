# README

# 프로젝트 생성과 기본 세팅

## ✏️ dependencies

- JDBC API
- H2 database
- lombok

[🔗 intellij 프로젝트 기본 세팅](https://github.com/choideakook/TIL/blob/main/Spring/0%20Spring%20TIL/Intellij%20프로젝트%20생성후%20기본%20세팅.md)

## ✏️ application.yml

```
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jdbc
    username: sa
    password:
    driver-class-name: org.h2.Driver
```

<br>

## ✏️ member Table 생성

- member Table 을 생성
- money column 생성
- row 2개 insert

```sql
create table member (
         member_id varchar(10),
         money integer not null default 0,
         primary key (member_id)
     );
     insert into member(member_id, money) values ('hi1',10000);
     insert into member(member_id, money) values ('hi2',20000);
```