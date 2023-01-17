# OSIV 와 성능 최적화

Open Session In View : hibernate

Open EntityManager In View : JPA

<br>

## ✏️ OSIV ON

<img width="500" alt="s02" src="https://user-images.githubusercontent.com/115536240/212784321-46bd8478-cd3a-442e-b048-22a8c5ed78d9.png">

- Spring application 을 실행하면 WARN 경고문이 출력된다.

```java
spring.jpa.open-in-view is enabled by default. Therefore, database queries may be performed during view rendering. Explicitly configure spring.jpa.open-in-view to disable this warning
```

- OSIV 전략은 Transcation 시작처럼 최초 DB 커넥션 시작 시점부터 API 응답이 끝날 때 까지 영속성 Context 와 DB 커넥션을 유지한다.
    - trascation 이 시작될 때 생성되어 고객이 요청이 종료되면 즉, 요청한 고객에게 DB 정보가 보여지면 소멸한다.
- 따라서 지금까지 View Template 나 API 컨트롤러에서 지연 로딩이 가능했던 것이다.
    - 지연로딩은 영속성 Context 가 살아있어야 가능하고, 영속성 Context 는 기본적으로 DB 커넥션을 유지한다.
- Entity 를 적극 활용해 LAZY 로딩 같은 기술을 Controller 나 View 에서 적극 활용할 수 있다는 장점을 가지고 있다.

<br>

### 📍 OSIV 전략의 단점

- 너무 오랜시간동안 DB 커넥션 리소스를 사용한다.
    - Controller 에서 외부 API 를 호출하면 외부 API 대기 시간 만큼 커넥션 리소스를 반환하지 못하고 유지해야한다.
    - 실시간 트래픽이 중요한 Application 에서는 커넥션이 모자라 장애로 이어질 수 있음.

<br>

## ✏️ OSIV OFF

 <img width="500" alt="s02" src="https://user-images.githubusercontent.com/115536240/212784313-1a9e72a0-816c-4528-ac55-dc210edaf5f2.png">


- OSIV 를 Off 하면 Transcation 범위 내에서만 영속성 Context  가 생존하게 된다.
    - Transcation 을 종료할 때 영속성 Context 를 닫고, DB 커넥션도 반환해 커넥션 리소스를 낭비하지 않는다.
- 하지만 영속성 Context 가 닫혀있기 때문에 LAZY 로딩을 실행할 수 없게된다.
- Transcation 안에서 지연로딩까지 처리하는 방법으로 이 문제를 해결할 수 있다.
    - 즉, 지금까지 작성한 많은 지연 로딩 코드를 Transcation 안으로 넣어야 하는 단점이 있다.
- view template 에서 지연로딩이 동작하지 않는다.
    - 결론적으로 Transcation 이 끝나기 전에 지연로딩을 강제로 호출해 두어야 한다.

<br>

### 📍 OSIV off 의 문제점 해결

Command 와 Query 를 분리해서 문제를 해결할 수 있다.

- Service 내의 별도의 Qurey 용 package 를 만들어 DTO 를 보관하고, Query Service 를 생성해 @Transcational 을 붙여준다.
- 이곳에서 Controller 에서 수행했던 로직을 실행하고 지연로딩과 관련된 모든 작업이 완료된 객체를 Controller 에서 호출한다.
