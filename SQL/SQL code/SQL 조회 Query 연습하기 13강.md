# SQL 조회 Query 연습하기 13강.

### 문제 : ***부서별 부서명 | 사원 리스트 | 평균연봉 | 최고연봉 | 사원수 조회***

- ***V 1.1***
    - join 사용 금지
    - if 문 사용

```sql
SELECT 
    if(deptId = 1, '홍보', '기획') as '부서명',
    GROUP_CONCAT(DISTINCT `name` ORDER BY id DESC SEPARATOR ', ') as '사원 리스트',
    truncate(avg(salary), 0) as '평균연봉',
    max(salary) as '최고연봉',
    min(salary) as '최소연봉',
    count(*) as '사원수'
FROM emp
GROUP BY deptId;
```

- ***V 1.2***
    - join 사용 금지
    - case 문 사용

```sql
select case
	when deptId = 1 then '홍보'
	when deptId = 2 then '기획'
	else '무소속'
	end as '부서명',

	group_concat(
		distinct name
		order by id desc 
    separator ', '
	) as '사원 리스트',
	
	truncate(avg(salary), 0) as '평균연봉',
	max(salary) as '최고연봉',
	min(salary) as '최소연봉',
	count(id) as '사원수'

from emp
group by deptId;
```

- ***V 2***
    - join 사용

```sql
select
	group_concat(
		E.name 
		order by E.id desc
		separator ', '
	) as '사원 리스트',
	
	truncate(avg(E.salary), 0) as '평균연봉',
	max(E.salary) as '최고연봉',
	count(E.id) as '사원수'
	
from emp as E
inner join dept as D
on E.deptId = D.id
group by deptId;
```