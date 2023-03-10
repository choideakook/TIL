# JVM, JRE, JDK 개념

## ✏️ JVM

***Java Virtual Machine - 자바 가상 머신***

### 📍 JVM 의 필요성

- 컴퓨터가 이해할 수 있는 기계어는 각각의 OS 에따라 다르다.
- JAVA 코딩을 맥 OS 의 기계어로 번역을 했더라도 윈도우나 리눅스 에서는 인식되지 않는다.
    - 즉, java 파일을 각각의 OS 에서 실행시키기 위해선 각각 OS 가 인식할 수 있는 기계어로 번역본을 만들어 주어야 한다는 의미아다.

### 📍 JVM 으로 문제 해결

- 모든 컴퓨터에 기본적으로 설치된 JVM 는 OS 가 인식할 수 있는 기계어를 번역해주는 역할을 한다.
    - OS 마다 전용으로 설치된 JVM 덕분에 개발자는 별도의 기계어를 번역하지 않아도 된다.

### 📍 기계어 번역의 절차

1. java 를 컴파일 하면 java 를 해석한 bite code 가 생성된다.
    - bite code 의 확장자는 .class 이다.
2. bite code 를 실행할 OS 에 설치된 JVM 이 전달 받고 OS 가 해석할 수 있는 기계어로 번역해준다.
    - JVM 은 java 뿐 아니라 다양한 프로그래밍 언어를 기계어로 번역 할 수 있다.

```java
java 언어 -- 컴파일 --> bite code -- JVM 번역 -> 기계어
```

⚠️ JVM 처럼 즉시 번역하는 방식을 JIT 컴파일러 라고 한다.

<br>

## ✏️ JRE

***Java Runtime Environment***

### 📍 JRE 와 JDK 의 차이점

- JDK 는 JRE 의 기능을 포함하며, 개발에 필요한 도구와 라이브러리를 추가로 제공한다.
- JRE 는 Java 애플리케이션을 실행하기 위한 최소한의 요구사항만 포함한다.
- JVM 과 개발자가 직접 구현하지 않은 Java 에서 기본적으로 제공하는 Class 의 라이브러리를 뜻한다.
    - List, Map, Set …
    - 더 자세히 설명하자면 Java 로 만든 소프트웨어가 컴파일후 실행이 될 때 소프트 웨어의 환경 요소들로서 필요한 것들을 뜻한다.

<br>

## ✏️ JDK

***Java Development Kit - 자바 개발 도구***

- JDK 는 JRE 를 포함한 세트 프로그램 이다.
    - JDK 를 설치하면 앞의 모든 기능을 사용할 수 있다.

```java
JDK > JRE >JVM
```

- JDK 는 JRE 의 기능 뿐 아니라 개발에 필요한 요소를 포함하고 있다.
    - javac - Java 코드를 컴파일 할 때 사용됨
    - jdb - java 를 디버깅 할 때 사용됨
    - jar - 서로 연관있는 Class 들을 하나의 JAR 파일로 묶어주는 기능
    - javaDoc - 문서화를 할 때 사용됨