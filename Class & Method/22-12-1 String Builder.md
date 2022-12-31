# String Builder 와 String Buffer
```java
String x = value1 + value2 + value3;
```
기본적인 string 의 경우 이렇게 변수를 설정할경우
v1 의 값 , v2 ,v3 의값 3개의 변수와  
v1 + v2 의 값을 가진 변수 , 거기에 v3를 더한 값을 가진변수  
총 5개의 변수가 필요하므로 데이터가 불필요하게 소진된다  
  
이를 방지하기 위해서 Builder 를 사용해 추가 변수 생성없이 문제를 처리할 수 있고  
더 나아가 String 의 값을 수정하는것도 가능하다  
  
* Buffer : 멀티 스트레드 환경에서 사용함  
* Builder : 단일 스트레드 환경에서 사용함  
  
# 선언 방법
```java
StringBuilder sb = new StringBuilder();  // 기본
StringBuilder sb = new StringBuilder(10);   // 10의 bit를 크기로하는 builder 생성
StringBuilder sb = new StringBuilder("Hello"); // Hello 를 저장하고있는 builder 생성
```
  
# Method
* append(...)               : 끝 부분에 ... 추가  
* insert(int offset , ... ) : 중간에 데이터를 삽입  
* reverse()                 : 데이터의 순서를 뒤집음 
* delete(int strat , int end)         : 범위 데이터 삭제  
* deleteCharAt(int index)             : 글짜 삭제  
* replace (int start , int end , ...) : 범위지정한 데이터를 ... 으로 변경  
* setCharAt (int index , char index ) : 특정 위치의 한글짜를 변경  
  
* toString() : 출력
  

