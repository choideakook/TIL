# select *,  쿼리 작동이 안될 때

- 시퀄 프로 query 또는 터미널 my sql 에 둘중 한가지를 입력하면 된다.
    - sql_mode 설정때문이라 my.ini나 my.cnf 파일에서 설정 바꿔줘도 된다고 한다.
    - global - 모든 설정을 변경하는 기능
        - 글로벌이 없다면 현제 접속하고 있는 프로젝트에만 적용된다.

```sql
SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
```

```sql
SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
```