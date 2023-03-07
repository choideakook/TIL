# Data Base 정규화

- 정제되지 않은 표를 관계형 DB 에 걸맞는 표로 변환 시켜주는 작업

## ✏️ 정규화란

수 많은 Data 가 담긴 raw 가 하나의 표에 담겨 불필요한 중복과 중첩으로 인해 Data 의 낭비가 심한 표를 분리해
중복을 없애고 편리하게 Data 를 저장하고 조회할 수 있는 형태로 바꾸는 작업

<br>

## ✏️ 제 1 정규화 - Atomic Columns

- 민트색으로 표시한 Tag Column 을 보면 하나의 Column 에 2개 이상의 값이 중첩 되어있다.
    - 이 방식으로 값을 채워 넣을 경우 정렬, 조회, join 등 여러가지 문제가 발생한다.

<img width="500" alt="sl1" src="https://user-images.githubusercontent.com/115536240/223582622-d4ca64b7-0c78-4fb1-8b83-0f2e7ca1e3f7.png">

### 📍 정보의 중첩을 제거하라

- 값이 중첩된 raw 를 각각 두개의 raw 나누어 하나의 column 에 하나의 값만 존재하게 만들어 준다
- ***raw 분리의 문제점***
    - tag 하나의 column 을 분리하기 위해 나머지 column 에서 똑같은 내용으로 중복이 발생된다.

### 📍 정보의 중복을 제거하라

- 하나의 표에서 표현하기엔 row 중복이 발생하기 때문에 표를 나누어 관리하는 방법으로 중복을 제거할 수 있다.
- ***연관관계 확인하기***
    - 하나의 topic 은 여러개의 tag 를 가질 수 있다.
    - 하나의 tag 은 여러개의 topic 을 가질 수 있다.
    - 즉, 두 표의 관계는 N : M 관계이다.
    - N : M 관계는 표로 표현할 수 없다.

### 📍 N : M 관계 사이에 서로를 매핑시켜주는 표를 만들어라

- 관계의 중간에 새로운 표를 만들어 N : 1 / 1 : M 관계로 변환시켜 표현하면 문제를 해결할 수 있다.
- ***제 1 정규화 완료***

<img width="500" alt="sl2" src="https://user-images.githubusercontent.com/115536240/223582637-b530c862-8cf8-4412-bdff-a106bed610fc.png">

<br>

## ✏️ 제 2 정규화 - No Partial Dependencies

표의 부분 종속성을 없애는 작업

- 표 내부에 중복키가 없어야 한다.

### 📍 부분 종속적 중복을 제거 해라

<img width="500" alt="sl3" src="https://user-images.githubusercontent.com/115536240/223582640-af9b3ac2-81cf-41bc-b359-ab3a0d999295.png">

- 제 1 정규화 이후의 표를 보면 몇몇 column 에서 부분적으로 중복이 발생하고 있다.
    - 잘 보면 부분적인 중복이 일어나는 column 은 하나의 my sql 에서만 발생되고 있다.
    - 이러한 형태의 중복을 부분 종속적인 중복이라고 한다.
- type 과 price 를 제외한 부분을 별도의 표로 나누어 부분 종속 중복을 제거할 수 있다.

<img width="500" alt="sl4" src="https://user-images.githubusercontent.com/115536240/223582642-66b62ee9-e31e-4475-88b1-e1d994cbcf16.png">

- 표를 다시 2개로 나누어 부분 종속 중복도 제거 했다.

<br>

## ✏️ 제 3 정규화 - No Transitive Dependencies

이행적 종속성을 없애는 작업

### 📍 이행적 종속성 중복을 제거하라

- 제 2 정규화 작업의 topic 표를 보면 author_id 부터 author_profile 까지의 값들이 중복되고 있다.
    - author_name 과 profile 은 id 에 의존하고 있다.
    - 이 부분을 다시 2개의 표로 나누어주면 중복을 제거 할 수 있다.

<img width="500" alt="sl5" src="https://user-images.githubusercontent.com/115536240/223582645-52a6c66c-71b5-41a6-813b-5951771f0b28.png">


- 제 3 정규화 작업을 끝으로 표 내의 모든 중복을 제거하는 것을 완료했다.
