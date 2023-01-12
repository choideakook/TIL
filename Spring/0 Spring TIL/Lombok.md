# Lombok

[🔗 Lombok 사용시 주의할 점](https://kwonnam.pe.kr/wiki/java/lombok/pitfall)

## ✏️ **TL;DR**

- @Data
    - getter, setter 만들어줌
- @AllArgsConstructor
    - 모든 필드값을 Parameter 로 받는 생성자를 만들어줌
- @NoArgsConstructor
    - Parameter 가 없는 기본 생성자를 만들어줌
- @RequiredArgsConstructor
    - DI 를 위한 생성자를 만들어줌

## ✏️ @Data

@Data 는 Lombok 의 종합 어노테이션인데 자주 사용하는 어노테이션을 모두 담고있다.

- Getter / Setter
- RequiredArgsConstructor
- ToString
- EqualsAndHashCode

### 📍 주의점

@Data 를 사용할 수 없는 경우는 개별 어노테이션을 사용해 해결할 수 있다.

- 이러한 파라미터와 같이 사용할 수 없다.
    - callSuper
    - includeFieldName
    - exclude