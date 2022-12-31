# Iterator

Colletion framework 에 포함되어있는 interface 이다.

배열 또는 배열과 유사한 자료구조를 순회하는 기능을 제공한다.

ex ) Set , List …

## ✏️ Iterator 의 특징

- 반복문과 다르게 index 를 순회하면서 index 를 제거할 수 있다.
- Colletion framework 에 소속된 Class 들 모두에게 적용 가능하다.
- 대량의 데이터를 처리할 때 효율이 떨어진다.

---

## ⚙️ Method

### 📍 hasNext()

Iterator 안의 값이 존재하는 지 확인후 boolean 으로 return 해주는 기능

### 📍 Next()

Iterator 안의 다음 값을 출력하고 출력한 값을 제거하는 기능

```java
HashSet<Integer> arr = new HashSet<>();

for (int i = 0; i < 5; i++)
    arr.add(i);

Iterator iterator = arr.iterator();
while (iterator.hasNext())
    System.out.print(iterator.next());  //01234
```

- arr 에 있는 값을 iterator 에 옮긴후 실행했기 때문에 arr 에게는 영향을 주지 않는다.

### 📍 remove()

Next() 로 출력하고 삭제한 값을 원래 배열에서도 삭제하는 기능

```java
HashSet<Integer> arr = new HashSet<>();

for (int i = 0; i < 5; i++)
    arr.add(i);

Iterator iterator = arr.iterator();
while (iterator.hasNext()) {
    System.out.print(iterator.next());  //01234
    iterator.remove();  // arr index 까지 삭제
}
for (Integer i : arr)
    System.out.println(i);   // 삭제됬기때문에 아무것도 출력되지않음
```