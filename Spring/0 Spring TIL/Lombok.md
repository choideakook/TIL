# Lombok

[πΒ Lombok μ¬μ©μ μ£Όμν  μ ](https://kwonnam.pe.kr/wiki/java/lombok/pitfall)

## βοΈΒ **TL;DR**

- @Data
    - getter, setter λ§λ€μ΄μ€
- @AllArgsConstructor
    - λͺ¨λ  νλκ°μ Parameter λ‘ λ°λ μμ±μλ₯Ό λ§λ€μ΄μ€
- @NoArgsConstructor
    - Parameter κ° μλ κΈ°λ³Έ μμ±μλ₯Ό λ§λ€μ΄μ€
- @RequiredArgsConstructor
    - DI λ₯Ό μν μμ±μλ₯Ό λ§λ€μ΄μ€

## βοΈΒ @Data

@Data λ Lombok μ μ’ν© μ΄λΈνμ΄μμΈλ° μμ£Ό μ¬μ©νλ μ΄λΈνμ΄μμ λͺ¨λ λ΄κ³ μλ€.

- Getter / Setter
- RequiredArgsConstructor
- ToString
- EqualsAndHashCode

### πΒ μ£Όμμ 

@Data λ₯Ό μ¬μ©ν  μ μλ κ²½μ°λ κ°λ³ μ΄λΈνμ΄μμ μ¬μ©ν΄ ν΄κ²°ν  μ μλ€.

- μ΄λ¬ν νλΌλ―Έν°μ κ°μ΄ μ¬μ©ν  μ μλ€.
    - callSuper
    - includeFieldName
    - exclude