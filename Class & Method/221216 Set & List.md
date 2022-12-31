# Set 과 List
[ [Collections Framework 의 구성] ](https://github.com/choideakook/Learn_Java/blob/main/Java_basic/221217%20Collections%20Framework%20의%20구성.md)
  
## ✏️ Set 과 List 의 특징

- Collection 을 상속하고 있는 interface 임 (Class 아님)
- Set
    - index 값 중복 불가
    - index 순서가 보장되지 않음
- List
    - index 값 중복 가능
    - add 한 순서대로 값이 저장됨

---

## ⚙️ Method

### 📍 contains()

특정 index 가 포함되어있는지 확인후 boolean 으로 return 해주는 기능

```java
ArrayList<Integer> set = new ArrayList<>();

for (int i = 0; i < 5; i++) set.add(i);

System.out.println(set.contains(3)); // true
System.out.println(set.contains(7)); // false
```

### 📍 containsAll()

cointains 는 배열과 index 를 비교하지만 All 은 배열대 배열로 비교한다.

```java
ArrayList<Integer> arr1 = new ArrayList<>();
ArrayList<Integer> arr2 = new ArrayList<>();

for (int i = 0; i < 5; i++)
    arr1.add(i);

arr2.add(3);
arr2.add(4);

System.out.println(arr1.containsAll(arr2)); // true
        
arr2.add(7);
        
System.out.println(arr1.containsAll(arr2)); // false
```

- 비교하는 배열의 index 중 하나라도 포함되는 것이 없다면 false

### 📍 retainAll()

두 배열에 공동으로 포함된 index 만 배열에 남기는 기능

```java
ArrayList<Integer> arr1 = new ArrayList<>();
ArrayList<Integer> arr2 = new ArrayList<>();

for (int i = 0; i < 5; i++)
    arr1.add(i);

arr2.add(3);
arr2.add(4);
arr2.add(7);

arr1.retainAll(arr2);

for (Integer i : arr1)
    System.out.print(i + " ");  // 3, 4
```

### 📍 removeAll()

괄호 안에 있는 배열의 index 값과 겹치는 index 값을 삭제하는 기능

```java
ArrayList<Integer> arr1 = new ArrayList<>();
ArrayList<Integer> arr2 = new ArrayList<>();

for (int i = 0; i < 5; i++) arr1.add(i);

arr2.add(3);
arr2.add(4);

arr1.removeAll(arr2);

for (Integer i : arr1)
    System.out.print(i + " ");  // 0, 1, 2
```
  
[ [Collections Framework 의 구성] ](https://github.com/choideakook/Learn_Java/blob/main/Java_basic/221217%20Collections%20Framework%20의%20구성.md)  
