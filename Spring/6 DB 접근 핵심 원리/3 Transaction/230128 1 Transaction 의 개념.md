# Transaction 의 개념

data 를 저장할 때 DB 에 저장하는 가장 대표적인 이유는 DB 에는 트랜잭션이라는 개념을 지원하기 때문이다.

## ✏️ Transaction

Transaction 하에서 DB 의 변경관련 작업이 이루어질 경우

로직 중간에 문제가 생기면 문제가 생기기 전에 변경된 부분을 DB 에 반영하지 않고 취소하는 기능이다.

모든 로직이 성공적으로 이루어질 경우에만 DB 에 처리된 결과를 적용하는 기능이다.

### 📍 Transaction 의 필요성

만약 A 의 계좌에서 B 의 계좌로 이체를 해야될 경우
풀어서 말하면 A계좌의 잔고를 줄이고, B 계좌의 잔고를 올리는 방식이다.

A 의 계좌에서 잔고를 줄인 뒤 B 계좌에서 error 가 발생할 경우
계좌이체는 실패하지만 A 의 계좌의 잔고는 줄어들어 버린다.

이러한 문제를 Transaction 이 방지할 수 있다.

## ✏️ Transaction ACID

Transaction 은 원자성 (Atomicity), 일관성 (Consistency), 격리성 (Isolation), 지속성 (Durability) 을 보장해야 한다.

### 📍원자성 Atomicity [🔗](https://github.com/choideakook/TIL/blob/main/Spring/6%20DB%20접근%20핵심%20원리/3%20Transaction/230128%205%20DB%20락%20개념의%20이해.md)

Transaction 내에서 실행한 작업은 마치 하나의 작업인 것처럼 모두 성공하거나 모두 실패하여야 한다.

### 📍일관성 Consistency

모든 Transaction 은 일관성 있는 DB 상태를 유지해야 한다.

- DB 에서 정한 무결성 제약 조건을 항상 만족해야 한다.

### 📍격리성 Isolation

동시에 실행되는 Transaction 들이 서로에게 영향을 미치지 않도록 격리해야 한다.

- 동시에 같은 Data 를 수정하지 못하도록 해야한다.
- 격리성은 동시성과 관련된 성능 이슈로 인해 Transaction 격리 수준 (Isolation level) 을 선택할 수 있다.

### 📍지속성 Durability

Transaction 을 성공적으로 끝내면 그 결과가 항상 기록되어야 한다.

- 중간에 시스템에 문제가 발생해도 DB log 등을 사용해 성공한 Transaction 내용을 복구해야 한다.

<br>

### 📍 Transaction 격리 수준
Isolation level

- Read Uncommited - 커밋되지 않은 읽기
    - 성능은 가장 좋지만 격리성이 가장 낮아 경리성이 잘 보장되지 않는다.
- Read Commited - 커밋된 읽기
    - 성능과 격리성이 적절해 가장 많이 사용되는 설정이다.
    - 강의 내용은 이 설정을 기준으로 한다.
- Repeatable Read - 반복 가능한 읽기
    - 성능과 격리성이 적절해 많이 사용되는 설정이다.
- Serializable - 직렬화 가능
    - 격리성이 가장 높지만 성능이 매우 낮아 잘 사용되지 않는다.
