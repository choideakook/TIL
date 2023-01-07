# 가변인자 (Varargs)

매소드의 parameter 를 고정적이 아닌 동적으로 설정하는 기능

## ✏️ 가변인자 사용하기

- 배열의 평균을 계산하는 Method 가 있다.

```java
static class Calculator {

        public void arrayAvg(double[] num) {

            double ni = 0;
            int nSize = num.length;

            for (double i : num) {
                ni += i;
            }

            double avg = ni / nSize;

            System.out.println("avg = " + avg);
        }
}
```

- 가변인자를 사용하지 않을 경우 이 method 를 사용하기위해서는 parameter 에 넣어줄 배열이 꼭 생성되어 있어야만 한다.

```java
		Calculator cal = new Calculator();
    double[] nums = {10, 20};
    
    cal.arrayAvg (nums); // avg = 15
```

<br>

- 가변인자를 사용하면 따로 배열을 생성하지 않아도 parameter 에 바로 원하는 값을 넣어서 method 를 실행시킬 수 있다.

```java
static class Calculator {
				// parameter 값에 [] 대신 가변인자 ... 로 수정해주면 된다.
        public void arrayAvg(double... num) {

            double ni = 0;
            int nSize = num.length;

            for (double i : num) {
                ni += i;
            }

            double avg = ni / nSize;

            System.out.println("avg = " + avg);
        }
}
```

- 가변인자를 사용하면 배열을 생성할 필요가 없다.

```java
		Calculator cal = new Calculator();
    
    cal.arrayAvg (20,30); // avg = 25
```