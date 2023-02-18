# MultiValueMap

[🔗 Collection](https://github.com/choideakook/Small_project/blob/main/Java_basic/221217%20Collections%20Framework%20의%20구성.md)

[🔗 Map](https://github.com/choideakook/TIL/blob/main/Class%20%26%20Method/22121%20Map.md)

Map 과 동일한 방식으로 작동되지만 하나의 Key 값에 복수의 Value 값이 입력될 수 있음

- Map
    - Key 값의 중복이 불가능하다.
    - 하나의 Key 값에 하나의 value 만 저장할 수 있다.
- MultiValueMap
    - 하나의 Key 값에 복수의 value 값을 저장할 수 있다.
    - Key 값을 get 하면 복수의 value 가 배열형태로 반환된다.

```java
  MultiValueMap<String, String> map = new LinkedMultiValueMap();
  map.add("keyA", "value1");
  map.add("keyA", "value2");

  //[value1,value2]
  List<String> values = map.get("keyA");
```

<br>

⚠️ MultiValueMap 은 순수 java 의 기능이 아닌 `springframework` 의 기능이다.