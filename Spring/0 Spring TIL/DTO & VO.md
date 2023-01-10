# DTO & VO

[🔗 자료 출처](https://youtu.be/z5fUkck_RZM)

## ✏️ DTO

### Data Transfer Object

데이터 전달용 객체

- Getter 와 Setter 를 이용해 계층간 데이터를 전할하는 객체이다.

```java
Controller (web)  <-get--- DTO (전달) ---set-> Service
```

- 데이터를 전달하기위해 오직 Getter Setter 만을 갖고, 다른로직은 갖지 않는다.
    - 순수하게 Data 전달만을 목적으로 하기 때문

<br>

### 📍 가변과 불변

- Setter 를 가질경우 언제든 필드값이 변경될 수 있어 이러한  DTO 는 가변적 이다.
- Setter 없이 DI 를 통한 필드 주입은 불변한 값을 보장받을 수 있어 이러한 DTO 는 불변적 이다.

<br>

### 📍 DTO Class 와 Entity Class 의 분리

Entity Class 는 DB 와 mapping 되어있는 핵심 Class 이다.

- Entity Class 는 절대로 응답값을 요청, 전달하는 Class 로 사용해서는 안된다.
- Service Class 들과 business 로직들이 Entitiy Class 를 기준으로 사용한다.
    - view 의 요청에 따라 Entity 가 변경 될 경우 많은 혼란이 발생할 수 있음
- 즉 view 의 변경에 따라 다른 class 에 영향을 주지 않는 DTO Class 를 이용해야 한다.

<br>

## ✏️ VO

### Value Object

값 그 자체를 표현하는 객체

<bt>

### 📍 DTO 와의 차이점

|  | DTO | VO |
| --- | --- | --- |
| 용도 | 레이어간 데이터 전달 | 값 자체 표현 |
| 동등 결정 | 속성값이 모드 같다고 해서 같은 객체가 아니다. | 속성값이 같으면 모두 같은 객체이다. |
| 가변 / 불변 | Setter 존재시 가변, 
Setter 비 존재 시 불변 | 불변 |
| 로직 | Getter / Setter 외의 로직을 갖지 않는다. | Getter / Setter 외의 로직을 가질 수 있다. |