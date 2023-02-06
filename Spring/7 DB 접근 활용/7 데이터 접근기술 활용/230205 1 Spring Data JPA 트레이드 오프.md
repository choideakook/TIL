# Spring Data JPA 트레이드 오프

## ✏️ 문제점

[🔗 예제 코드](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/5%20Spring%20Data%20Jpa/230204%202Spring%20Data%20JPA%20적용.md)

<img width="520" alt="s7521" src="https://user-images.githubusercontent.com/115536240/216801645-f4410d3d-5e1f-4391-98eb-f7964b40d2f6.png">

- 지금 상황에서는 OCP 의 원칙을 준수하기 위해 `ItemService` 가 `SpringDataJpaItemRepository` 를 바로 의존하지 않고,
`ItemRepository` 를 기존 그대로 의존해 `JpaItemReposiroyV2` 라는 어뎁터 역할의 구현체를 거처서 `SpringDataJpaItemRepository` 를 의존하고 있다.
- 결론적으로 OCP 의 원칙을 준수하기 위해 불필요한 과정이 너무 많이 생겨버렸다.
    - 유지보수 관점에서 어뎁터 역할의 Class 와 실제 핵심 로직이 있는 코드까지 해야한다는 어려움이 있다.

## ✏️ OCP, DI 원칙을 포기해서 문제 해결

OCP 와 DI 원칙을 포기하고 `ItemService` 에서 바로 `SpringDataJpaItemRepository` 를 의존하면 불필요한 interface 와 구현체를 사용하지 않아 구조적으로 간결해지는 결과를 얻을 수 있다.

- 하지만 이 방법을 적용하려면 Service 코드를 수정하고,
추후에 다른 기술로 교체하게 되더라도 코드를 전부 수정해야하는 문제점이 있다.

<img width="530" alt="s7711" src="https://user-images.githubusercontent.com/115536240/216855581-36b0726d-4699-4387-b606-09f2c95b9c7f.png">

- 실질적인 런타임 객체 의존 관계도 훨신 간결해 진다.

<img width="534" alt="s7712" src="https://user-images.githubusercontent.com/115536240/216855589-6ce4a147-d0ea-4c9a-ab5b-7f9414cf5ec4.png">

<br>

### 📍 장단점

- DI, OCP 를 지키기 위해 어뎁터를 도입하고, 더 많은 코드를 유지할 것인지
- 어뎁터를 제거하고 구조를 단순하게 해서, DI, OCP 의 원칙을 깨고 Service 코드를 수정할 것인지

결국 이 두가지 방식은 트레이드 오프 관계에 있다.

- 구조적 안정성 vs 단순한 구조와 개발의 편의성
- 두가지 방식모두 상황에 따라 더 나은 선택이 될 수 있다.
