# UnsupportedOperationException

Unsupported / Operation / Exception

지원되지 않는 / 작업 / 예외

직역하자면 내가 만든 로직이 지원될 수 없다는 의미 같다.

<br>

## ✏️ 발단, 원인

알고리즘 테스트를 풀던중 의도치않은곳에서 처음보는 예외가 발생했다.

```java
        String[] cards1 = {"i", "drink", "water"};

        List<String> list1 = Arrays.asList(cards1);

        list1.remove(0); // 문제의 원인
```

예제로 준비된 배열을 작업하기 편하게 List 로 변경해 준뒤,

인덱스를 제거하니 해당 예외가 발생했다.

```java
java.lang.UnsupportedOperationException
```

예외의 종류만 나오고 코맨트도 없어서 간단한 문제라고 생각했는데

문제가 없는 코드인것같아서 원인 파악이 어려웠다.

구글링해본 결과

Array.asList 로 생성한 list 는 값이 고정되어 있어 원소를 제거할 수 없다고 한다.

<br>

## ✏️ 해결

꼭 List 를 사용해야되겠다면 이 방법 말고 다른방법을 사용해야 될 것 같다.

딱히 방법이 생각나지 않아 for 문을 사용해 수동으로 인덱스를 옮겼다.

```java
List<String> list1 = new ArrayList<>();

for (String s : cards1) list1.add(s);
```