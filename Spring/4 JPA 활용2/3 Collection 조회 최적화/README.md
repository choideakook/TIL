# API 개발 고급 - Collection 조회 최적화

## ✏️ 1 : N 관계에서의 성능최적화 방법

1 : N 의 경우 하나의 data 를 조회해도 딸려오는 data 가 많은 양이 될 수 있어 ~ : 1 의 관계와 최적화 방법이 약간 다르다.

- Order 기준으로 OrderItem 과 Item 을 조회를 해볼 예정이다.
    - 주문 내역과 상품 내용 조회

## ✏️ **TL;DR**

1. 먼저 Entity 를 DTO 로 조회
    1. Fetch Join 으로 최적화
    2. Collection 최적화
2. paging 이 필요할 경우 hibernate.default_batch_fetch_size 로 촤적화
    1. paging 필요없으면 1-b 에서 종료
    2. 그래도 언젠가 필요하게 될 지 모르니 2번을 기본값으로 만들어 놓는게 좋을것같다.
3. 그래도 성능이 안나올 경우 JPA 에서 DTO 로 바로 조회하는 방식 사용
4. 그래도 해결이 안될경우 NativeSQL or JdbcTemplate 사용