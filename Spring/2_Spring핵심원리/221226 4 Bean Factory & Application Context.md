# Bean Factory & Application Context

## ✏️ Bean Factory 와 Application Context 구조

- 두가지를 통틀어 Spring Container 라고 부른다.

![컨테이너의구조.PNG](Bean%20Factory%20&%20Application%20Context%20766a769bdf88422085c042256eec3da3/%25E1%2584%258F%25E1%2585%25A5%25E1%2586%25AB%25E1%2584%2590%25E1%2585%25A6%25E1%2584%258B%25E1%2585%25B5%25E1%2584%2582%25E1%2585%25A5%25E1%2584%258B%25E1%2585%25B4%25E1%2584%2580%25E1%2585%25AE%25E1%2584%258C%25E1%2585%25A9.png)

## ✏️ Bean Factory

- Spring Container 의 최상위 interface 이다.
- Spring Bean 을 관리하고 조회하는 역할을 담당
- getBean () 기능을 재공

## ✏️ Application Context

- Bean Factory 의 기능을 모두 상속받아 제공
    - Bean Factory ********를 직접 사용할 일은 거의 없음********
- Application Context 는 빈 관리기능 + 편리한 부가기능을 제공
    
    ![부가기능제공.PNG](Bean%20Factory%20&%20Application%20Context%20766a769bdf88422085c042256eec3da3/%25E1%2584%2587%25E1%2585%25AE%25E1%2584%2580%25E1%2585%25A1%25E1%2584%2580%25E1%2585%25B5%25E1%2584%2582%25E1%2585%25B3%25E1%2586%25BC%25E1%2584%258C%25E1%2585%25A6%25E1%2584%2580%25E1%2585%25A9%25E1%2586%25BC.png)
    
    - Message source 를 활용한 국제화 기능
        - 한국에서 들어오면 한국어로 / 영어권에서 들어오면 영어로 출력함
    - 환경변수
        - 로컬, 개발, 운영등을 구분해서 처리
    - 애플리케이션 이벤트
        - 이벤트를 발행하고 구독하는 모델을 편리하게 지원
    - 편리한 리소스 조회
        - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회