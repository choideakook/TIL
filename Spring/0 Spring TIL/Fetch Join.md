# Fetch Join

SQL 에서 지원하는 기능이 아닌 JPQL 에서 성능 최적화를 위해 제공되는 기능

- 연관관계에 있는 Entity 나 Collection 을 한번에 같이 조회할 수 있다.

<br>

## ✏️ Entity Fetch Join ( ~ : 1 연관관계 )

Member Entity 와 Order Entity 가 서로 연관관계에 있다.

- Member

```java
@Entity
@Getter @Setter
@NoArgsConstructor (access = AccessLevel.PROTECTED)
public class Member {

    @Id @GeneratedValue
    @Column (name = "member_id")
    private Long id;

    @NotEmpty
    private String name;

    @Embedded
    private Address address;

    @OneToMany (mappedBy = "member") 
    private List<Order> orders = new ArrayList<>();
```

<br>

- Order

```java
@Entity
@Table (name = "orders")
@Getter @Setter
@NoArgsConstructor (access = AccessLevel.PROTECTED)
public class Order {

    @Id @GeneratedValue
    @Column (name = "order_id")
    private Long id;

    @ManyToOne (fetch = FetchType.LAZY)
    @JoinColumn (name = "member_id")
    private Member member;

    @OneToMany (mappedBy = "order" , cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne (fetch = FetchType.LAZY , cascade = CascadeType.ALL)
    @JoinColumn (name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate;
```

<br>

Member 를 조회하면서 연관된 Order Entity 도 함께 조회하는 JPQL 을 작성하려면 Fetch Join 문이 필요하다.

```java
public List<Member> findAllWithOrder(){
    return em.createQuery(
        "select m from Member m" +
        " join fetch m.order o", Member.class
    ).getResultList();
```

작성된 JPQL 문에서는 fetch 가 사용됬지만 실제 실행된 SQL 문에서는 fetch 가 사용되지 않는다.

```sql
SELECT MEMBER,ORDER FROM MEMBER INNER JOIN ORDER ON ORDER_ID = ORDER_ID
```

<br>

### 📍 fetch join 의 장점

fetch join 을 사용하지 않고 findAll 로 DB 를 조회할 경우 지연로딩으로 인해 영속성 Context 가 별도의 Query 문을 통해 연관관계에 있는 DB 를 호출해야한다.

이때 로직이 정말 필요한 하나의 값만 호출하는 Query 문을 작성하기 때문에 N+1 문제가 발생해버린다.

<br>

fetch join 은 사전에 연관관계에 있는 DB 까지 미리 조회하기 때문에 별도의 Query 문이 필요 없어지게 되고 네트워크 용량 면에서 크게 개선할 수 있게된다.

<br>

## ✏️ Collection Fetch Join ( 1 : N 연관관계 )

~ : 1 의 상황과 다르게 1 : N 의 연관 관계일 경우 하나의 필드에 다양한 값들이 들어갈 수 있어 결과가 증가되는 (결과가 뻥튀기됨) 상황이 발생한다.