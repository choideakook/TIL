# SQL 의 Join 문

관계형 데이터베이스에서 여러개의 Table 을 엮어 하나의 Table 로 만드는 기능

- link 없으면 web 이 아니듯, join 이 없다면 그것은 관계형 DB 가 아니라고 할만큼 중요한 비중을 차지한다.
- JPQL 에서도 join 을 지원하고 JPQL 의 문법에 맞게 SQL 처럼 사용하면 동일한 방식으로 사용이 가능하다.

<br>

## ✏️ Join 의 필요성

- table

| id (PK) | title | description | name | city |
| --- | --- | --- | --- | --- |
| 1 | HTML | HTML is … | egoing | jeju |
| 2 | CSS | CSS is … | leezche | jeju |
| 3 | Database | Database is .. | egoing | jeju |

name 과 city Colums 에서 중복이 발생되고있다.

이 때 중복된 부분에 수정이 필요하다면 data 를 하나하나 모두 바꿔줘야하는 비요율이 발생한다.

이 문제를 해결하기위해 Table 을 나눠줄 수 있다. 

- table1

| id (PK) | title | description | name (FK) | city (FK) |
| --- | --- | --- | --- | --- |
| 1 | HTML | HTML is … | 1 | 1 |
| 2 | CSS | CSS is … | 2 | 1 |
| 3 | Database | Database is .. | 1 | 1 |
- table2

| id (PK) | name_id |
| --- | --- |
| 1 | egoing |
| 2 | leezche |
- table3

| id (PK) | city_id |
| --- | --- |
| 1 | jeju |

이렇게 Table 을 나눠주면 data 가 변경될 경우 한번의 수정으로 모든 중복되는 data 를 변경할 수 있게된다.

하지만 사람이 봤을 때 가독성이 매우 떨어지는 문제가 생기는 단점이 생긴다.

이러한 두가지 방식의 장단점에서 단점은 버리고 장점만 사용할 수 있게 하는 기능을 Join 이 재공한다.

<br>

## ✏️ Left Join

SELECT * FROM table1 LEFT JOIN table2 ON table1.name_id = table2.id

- FROM table1 LEFT JOIN table2
    - topic 을 왼쪽에 기준으로 두고 오른쪽에 autheor 를 배치해라
- ON table1.name_id = table2.id
    - table1 의 name_id 컬럼과 table2 의 id 컬럼을 참고해서 두 Table 을 하나로 만들어라

| id (PK) | title | description | name (FK) | id (PK) | name_id | city (FK) |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | HTML | HTML is … | 1 | 1 | egoing | 1 |
| 2 | CSS | CSS is … | 2 | 2 | leezche | 1 |
| 3 | Database | Database is .. | 1 | 1 | egoing | 1 |

❗️만약 table1 에만 존재하는 FK 가 있을경우 table2 에는 null 이 출력된다.

- left join 대신 inner join 을 사용하면 null 값을 제외하고 출력해준다.
    - inner join 은 가장 많이 사용되는 기능이고 앞의 inner 를 생략해 그냥 join 으로만 사용가능하다.

❗️ FK 와 PK 가 거슬리면 Select 에서 포함을 시키지 않으면 출력되지 않는다.

❗️ JPQL 에서도 동일한 방식으로 작동이 되지만  자동으로 FK 와 PK 를 걸러서 출력하고 ,

ON table1.name_id = table2.id 처럼 따로 FK 와 PK 를 연결하지 않아도 자동으로 연결해준다.