# Spring Data JPA

## ✏️ Spring Data JPA 의 주요 기능

Spring Data JPA 는 JPA 를 편리하게 사용할 수 있도록 도와주는 라이브러리이다.

수 많은 기능을 제공하지만 가장 대표적인 기능은 2가지 이다.

1. 공통 인터페이스 기능
2. 쿼리 메서드 기능

<br>

## ✏️ 1. 공통 인터페이스 기능

- JpaRepository 인터페이스를 통해서 기본적인 CRUD 기능을 제공한다.
    - CRUD 뿐 아니라 공통화 가능한 거의 모든 기능이 포함되어 있다.

![s7511.png](Spring%20Data%20JPA%206f139d6894b24863b2ae932003846e2d/s7511.png)

<br>

### 📍 JpaRepository 사용 방법

- JpaRepository 인터페이스를 인터페이스로 상속받고, 제네릭에 관리할 Entity , Entity ID 를 값으로 넣어주면 된다.
    - 이렇게 하면 JpaRepository 가 관리하는 공통 기능을 모두 사용할 수 있다.

```java
// 아래 처럼 JpaRepository 를 상속하면 사용이 가능하다.
public interface ItemRepository extends JpaRepository <Member, Long>{
}
```

- 상속 받은 인터페이스는 Spring Data JPA 가 자동으로 구현 Class (Proxy Class) 를 생성해준다.
    - 그리고 만든 구현 Class 의 instance 를 만들어 Spring Bean 으로 등록까지 해준다.
    - 개발자는 구현 Class 없이 interface 만으로 공통 기능들을 사용할 수 있게된다.

![s7512.png](Spring%20Data%20JPA%206f139d6894b24863b2ae932003846e2d/s7512.png)

<br>

## ✏️ 2. Query Method 기능

Spring Data JPA 는 인터페이스에 Method 만 적어두면,
Method 이름을 분석해 Query 를 자동으로 만들고 실행해주는 기능을 제공한다.

- 순수 JPA 를 사용하면 직접 JPQL 을 작성하고,
Parameter 바인딩도 해야한다.

```java
public List<Member> findByUsernameAndAgeGreaterThan(String username, int age) {
      return em.createQuery
          ("select m from Member m where m.username = :usernameand m.age > :age")
              .setParameter("username", username)}
```

- Spring Data JPA 는 JPQL 없이 Method 이름을 분석해 자동으로 필요한 JPQL 을 만들고 실행해준다.
    - Method 이름은 아무렇게나 써도 되는건 아니고 양식에 맞게 작성해야 한다.

```java
public List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
}
```

<br>

### 📍 Spring Data JPA 가 제공하는 Query Method 기능

[🔗 Query Method 스프링 데이터 JPA 공식 문서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)

[🔗 Query Method (페이징) 스프링 데이터 JPA 공식 문서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.limit-query-result)

- 조회
    - findBy ~
    - readBy ~
    - queryBy ~
    - getBy ~
- Count (반환타입 : long)
    - countBy (반환타입 : long)
- Exists (반환타입 : boolean)
    - existsBy
- 삭제 (반환타입 : long)
    - deleteBy ~
    - removeBy ~
- Distinct
    - findDistinct
    - findMemberDistinctBy ~
- Limit
    - findFirst3
    - findFirst
    - findTop
    - findTop3

### 📍 JPQL 직접 작성하기

- Method 명이 너무 길어 가독성이 떨어진다고 판단될 때는 직접 JPQL 을 적어서 문제를 해결할 수도 있다.
    - 이때 Method 이름으로 실행되는 규칙은 무시된다.
- Spring data jpa 는 JPQL 뿐아니라 SQL 도 지원해서 네이티브 Query 를 JPQL 대신 입력해도 작동된다.

```java
@Query("select i from Item i where i.itemName like :itemName and i.price<= :price")
public List<Item> findItems(@Param("itemName") String itemName, @Param("price") Integer price){
}

```