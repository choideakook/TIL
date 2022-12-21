# @Entity import 오류

## ✏️ 발단

log in page 만들기를 연습하던중

service 의 join 기능 test 를 실행하니 

```
Failed to load ApplicationContext
```

이런 오류가 발생했다.

확인해보니 각 class 마다 애노테이션이 제대로 처리되어있지 않아서 발생한 에러라고 해서 class 를 하나하나 확인해보니

Member class 에 @Entity 가 누락되어 있었다.

## ✏️ 1차 시도 (실패)

Entity 를 입력해보니 자동 완성기능에 Entity 가 없었다.

class 맴버들에게 붙여줘야하는 @Id / @GeneratedValue 또한 인식되지 않았다.

## ✏️ 2차 시도 (실패)

Entity 를 import 하는 단축키를 입력해봐도 불러올 수 없어서 수동으로 직접 타이핑해서 import 를 하려고 했다.

```
import javax.persistence.Entity;
```

당연히 실패했지만 persistence 이 부분에서 import 를 할 수 없다는 걸 알아냈다.

## ✏️ 3차 시도 (성공)

지금까지 단서를 토대로 구글링해본결과 나와 100% 동일한 문제를 겪는 사람을 발견했다.

[링크](https://www.inflearn.com/questions/400589/javax-persistence-enetity-import-안-됩니다)

해결 방법을 읽어보니 persistence 는 jpa 에 소속된 녀석 이라는것같다.

build.gradle 을 확인해보니 역시 jpa 가  없었다.

제대로 implementation 해준 후 오른쪽 Gradle 창에서 persistence 가 업데이트 되어있는걸 확인 후

Member Class 로 돌아가보니 정상적으로 어노테이션이 생성된걸 볼 수 있었다.