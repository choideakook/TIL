# 구현 요구사항

- 회원 기능
    - 회원 등록
    - 회원 조회
- 상품 기능
    - 상품 등록
    - 상품 수정
    - 상훔 조회
- 주문 기능
    - 상품 주문
    - 주문 내역 조회
    - 주문 취소

<br>

❗️ 구현하지 않은기능

- 로그인과 권한 관리
- 파라미터 검증과 예외처리 단순화
- 상품은 도서만 사용
- 카테고리 사용 안함
- 배송 정보 사용 안함

<br>

## ✏️ 애플리케이션 아키텍처

<img width="500" src="https://user-images.githubusercontent.com/115536240/211179872-1f4458f6-ca6c-48c7-ae3f-2ef484c9cc98.png">

### 📍 계층형 구조 사용

- Controller , web : 웹 계층
- service : 비지니스 로직 , 트랜잭션 처리
- repository : JPA 를 직접 사용하는 계층 , Entity manager 사용
- domain : Entity 가 모여있는 계층 , 모든 계층에서 사용됨

<br>

## ✏️ 개발 순서

- Service , Repository 계층 개발  
    - [🔗 Repository 개념](https://github.com/choideakook/TIL/blob/main/Spring/7%20DB%20접근%20활용/4%20JPA/230203%201%20JPA%20개발.md)  
- Test Cast 로 검증
- Web 계층 개발 , 적용
