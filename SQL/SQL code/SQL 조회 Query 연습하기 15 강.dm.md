# SQL 조회 Query 연습하기 15 강.

## ✏️ 추가 문제 - 부서명 | 사번 | 입사일 | 이름 |  연봉

- 홍길동의 연봉을 6000 으로 변경
- 각 부서별 최고연봉자(1명)의 정보를 출력한다.
    - 만약에 부서별로 최고 연봉자가 2명 이상 이라면 번호가 빠른순으로 1명

```sql
SELECT *
FROM (
    SELECT D2.name AS `부서명`,
    E.id AS `사원번호`,
    E.name AS `사원명`,
    DATE(E.regDate) AS `입사일`,
    CONCAT(FORMAT(E.salary, 0), '만원') AS `연봉`
    FROM emp AS E
    INNER JOIN (
        SELECT E.deptId AS id,
        MAX(E.salary) AS maxSalary
        FROM emp AS E
        GROUP BY E.deptId
    ) AS D
    ON E.deptId = D.id
    AND E.salary = D.maxSalary
    INNER JOIN dept AS D2
    ON E.deptId = D2.id
    ORDER BY `입사일`, `사원번호` DESC
    LIMIT 3
) AS E
GROUP BY E.`부서명`
```

<br>

## ✏️ 15 강

### 📍 [부서명, 사원번호, 사원명] 양식으로 모든 사원 출력

- ***조건 : IT부서는 [IT, NULL, NULL] 으로 출력***
    - ***Left & Right join***
        - inner join 은 두 table 이 공통된 부분만 mapping 시켜준다면
        - left 와 right join 은 하나의 table 을 기준으로 해서 mapping 시키는 기능이다.

```sql
SELECT
	D.name as '부서명',
	E.id as '사원번호',
	E.name as '사원명'
from emp as E

right join dept as D
on E.deptId = D.id
order by E.id;
```

### 📍 전 사원에 대하여, [부서명, 사원번호, 사원명] 양식으로 출력

- ***조건 : IT부서는 [IT, 0, -] 으로 출력***
    - ***IFNULL 함수 사용***
        - 만약 값이 없을경우 null 이 아닌 다른 값을 출력시켜주는 기능
        - IFNULL ( {column} , {대체} )

```sql
select
	D.name as '부서명',
	ifnull(E.id, 0) as '사원 번호',
	ifnull(E.name, '    -') as '사원명'
from emp E

right join dept D
on D.id = E.deptId;

```

<br>

### 📍 모든 부서별, 최고연봉, IT부서는 0원으로 표시

```sql
select
	D.name as '부서명',
	max(ifnull(E.salary , 0)) as '최고연봉'
from emp E

right join dept D
on D.id = E.deptId
group by D.id;
```

### 📍 모든 부서별, 최저연봉, IT부서는 0원으로 표시

```sql
select
	D.name as '부서명',
	min(ifnull(E.salary , 0)) as '최고연봉'
from emp E

right join dept D
on D.id = E.deptId
group by D.id;
```

### 📍 모든 부서별, 평균연봉, IT부서는 0원으로 표시

```sql
select
	D.name as '부서명',
	truncate(avg(ifnull(E.salary , 0)), 0) as '최고연봉'
from emp E

right join dept D
on D.id = E.deptId
group by D.id;
```

### 📍 하나의 쿼리로 최고액연봉자와 최저역연봉자의 이름과 연봉

- UNION 함수를 사용해 문제 해결
- ***NUION***
    - 두개의 Query 를 하나의 Query 로 만들어 주는 함수
    - union 된 query 가 동일한 내용일 경우 table 로 출력될 때 스킵된다.
- ***UNION ALL***
    - union 과 달리 ALL 을 사용하면 중복된 내용까지 모두 table 에 출력된다.

⚠️ union 으로 연결하는 query 문은 꼭 괄호로 묶어 주어야 한다.

```sql
(
    SELECT E.salary AS `연봉`,
    E.name AS `사원명`,
    '최고연봉자' AS `타입`
    FROM emp AS E
    ORDER BY E.salary DESC
    LIMIT 1
)
UNION ALL
(
    SELECT E.salary AS `연봉`,
    E.name AS `사원명`,
    '최저연봉자' AS `타입`
    FROM emp AS E
    ORDER BY E.salary ASC
    LIMIT 1
)
ORDER BY `타입` ASC;
```