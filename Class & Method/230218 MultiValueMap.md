# MultiValueMap

[πΒ Collection](https://github.com/choideakook/Small_project/blob/main/Java_basic/221217%20Collections%20Framework%20μ%20κ΅¬μ±.md)

[πΒ Map](https://github.com/choideakook/TIL/blob/main/Class%20%26%20Method/22121%20Map.md)

Map κ³Ό λμΌν λ°©μμΌλ‘ μλλμ§λ§ νλμ Key κ°μ λ³΅μμ Value κ°μ΄ μλ ₯λ  μ μμ

- Map
    - Key κ°μ μ€λ³΅μ΄ λΆκ°λ₯νλ€.
    - νλμ Key κ°μ νλμ value λ§ μ μ₯ν  μ μλ€.
- MultiValueMap
    - νλμ Key κ°μ λ³΅μμ value κ°μ μ μ₯ν  μ μλ€.
    - Key κ°μ get νλ©΄ λ³΅μμ value κ° λ°°μ΄ννλ‘ λ°νλλ€.

```java
  MultiValueMap<String, String> map = new LinkedMultiValueMap();
  map.add("keyA", "value1");
  map.add("keyA", "value2");

  //[value1,value2]
  List<String> values = map.get("keyA");
```

<br>

β οΈΒ MultiValueMap μ μμ java μ κΈ°λ₯μ΄ μλ `springframework` μ κΈ°λ₯μ΄λ€.