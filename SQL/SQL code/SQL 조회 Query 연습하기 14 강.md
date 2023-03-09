# SQL 조회 Query 연습하기 14 강.

## ✏️ ***문제 : 부서명 | 사원명 | 사원입사일 | 연봉***

각 부서별 최고연봉자의 이름과 입사날짜를 출력하시오

### 📍 1단계

- 양식에 맞는 전체 사원 정보 조회하기
    - 1단계 처럼 1차원 적인 방식으로는 문제를 해결할 수 없음

```sql
select 
	D.name as '부서명',
	E.name as '사원명',
	date(E.regDate) as '입사일',
	concat(
		format(E.salary, 0),
		' 만원'		
	) as '연봉'
from emp as E
inner join dept as D
on E.deptId = D.id;
```

### 📍 2단계

- 사원명 | 연봉 | 부서 | 최고 연봉 조회하기
    - 서브쿼리로 부서 id 와 최고 연봉을 조회하는 쿼리를 만듬
    - join 문에 서브쿼리를 넣고 부서 id 로 연결 시킴
    - 결과적으로는 모든 사원 에게 부서별 최고 연봉 table 이 join 됨

```sql
select
	E.id,
	E.name,
	E.salary,
	D.id as deptId,
	D.maxSalary
from emp as E
inner join(
	select 
		E.deptId as id,
		Max(E.salary) as maxSalary
	from emp as E
	group by E.deptId
) as D
on D.id = E.deptId;
```

### 📍 3단계

- 2 단계에서 만든 table 에서 where 절을 사용해
salary 와 maxSalary 가 동일한 row 만 조회 한다.

```sql
select
	E.id,
	E.name,
	E.salary,
	D.id as deptId,
	D.maxSalary
from emp as E

inner join(
	select 
		E.deptId as id,
		Max(E.salary) as maxSalary
	from emp as E
	group by E.deptId
) as D
on D.id = E.deptId

where E.salary = D.maxSalary;
```

### 📍 4 단계

- 3 단계 에서 얻은 raw 를 문제의 양식에 맞게 정리
    - 사원 명을 가져오기 위해서 dept table 을 한번 더 join

```sql
select
	D2.name as '부서명',
	E.name as '사원명',
	date(E.regDate) as '입사일',
	concat(format(E.salary, 0), ' 만원') as '연봉'
from emp as E

inner join(
  # 서브쿼리 - 부서 번호와 부서내 최고 연봉 을 조회
	select 
		E.deptId as id,
		Max(E.salary) as maxSalary
	from emp as E
	group by E.deptId
) as D
on E.deptId = D.id
# 사원의 연봉과 최고 연봉이 같을 경우만 조회
and E.salary = D.maxSalary

# 부서명을 조회하기 위해 추가 join
inner join dept as D2
on E.deptId = D2.id;
```

<br>