# SOLID

## ✏️좋은 객체 지향 설계의 5가지 원칙 SOLID

- SRP : 단일 책임 원칙
- OCP : 개방 , 폐쇄 원칙
- LSP : 리스코프 차환 원칙
- ISP : 인터페이스 분리 원칙
- DIP : 의존관계 역전 원칙

### 📍 SRP : 단일 책임 원칙

하나의 Class 는 하나의 책임만 가져야 한다.

- 코드의 변경이 있을 때 변경으로 인한 파급 효과가 적다면 단일 책임 원칙을 잘 따른것
- 범위를 너무 작게 하면 코드가 너무 잘게 쪼개지고 범위를 크게하면 책임이 너무 많아져 적절하게 조절할 수 있는 능력이 필요하다.

### 📍 OCP : 개방 , 폐쇄 원칙 (중요)

소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.

- 새로운 기능을 확장할 때 기존 로직을 변경하지말고 새로운 Class 를 생성해 로직을 구현한다.

❗️ 실질적으로 기능이 확장될 경우 클라이언트에서 기존 class 에서 새로운 class 를 의존할 수 있게 변경해줘야하는데 이렇게 되면 OCP 원칙이 깨지게 된다.

이러한 문제를 해결할 수 있는것이 Spring (Spring container)이다.

ex) 기존 코드에서 새로운 Class 로 변경하려면 Service 의 코드를 수정해야 함

```
@Service
public MemberService {

		MemberRepository repository = new MemoryRepository(); // 기존 코드
		MemberRepository repository = new JpaRepository(); // 변경 코드
...
```

### 📍 LSP : 리스코프 차환 원칙

프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.

- Class 를 변경하더라도 사회적으로 합의된 기준에 맞게 기능을 구현해야 한다.
    
    ex) 로그인 기능을 확장했는데 이름만 로그인이고 실질적 기능은 탈퇴기능이면 안된다.
    
    로그인을 했을때 어떻게 되더라도 로그인이 되었다면 원칙을 준수한 것
    

### 📍 ISP : 인터페이스 분리 원칙

클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

- 각각의 기능을 하는 Method 들을 비슷한것끼리 묶어 Class 화해서 잘 정리해야 한다.
- ISP 를 잘 지킬경우 한가지 Class 를 변경해도 다른 Class 에 영향을 주지 않고 인터페이스가 명확해지며, 대체 가능성이 높아진다.

### 📍 DIP : 의존관계 역전 원칙 (중요)

프로그래머는 “추상화에 의존해야지, 구체화에 의존하면 안된다.” 의존성 주입은 이 원칙을 따르는 방법 중 하나다.

- Memory Member Repoistory 는 interface 인 Member Repository 에만 의존해야지 service 나 controller 등등 다른것에 대해서는 몰라야 한다는 의미
- 철저하게 자기 자신의 역할에만 의존하게 해야한다.
    
    ex) 아래 예제에서는 Service 가 Member Repository 만 의존해야 하지만 Memory 나 JPA 까지 의존하고있다.
    
    이경우 DIP 를 위반하게 된다.
    

```
@Service
public MemberService {

		MemberRepository repository = new MemoryRepository(); // 기존 코드
		MemberRepository repository = new JpaRepository(); // 변경 코드
...
```

## ✏️ 정리

- 모든 설계에 역활(interface)과 구현을 분리해야한다.
- 다형성 만으로는  예제의 상황 때문에 OCP 와 DIP 원칙을 지킬 수 가없다.
    - spring container 가 원칙을 지키면서 코딩을 효율적으로 할 수 있게 도와준다.
