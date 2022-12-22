# Application Run 이 안되는 error

평범하게 강의를 들으며 코딩을 하던중 web page 에서 확인을 하려고 main method 를 실행해보니 에러코드와 함께 컴파일이 바로 종료되버렸다

- 에러코드

```java
Error starting ApplicationContext. 
To display the conditions report re-run your application with 'debug' enabled.

2022-12-22 19:38:21.853 ERROR 33397 : Application run failed
```

## ✏️ 첫번째 시도 (실패)

혹시 포트 충돌인가 싶어 [application.properties](http://application.properties) 에서 8080 에서 8081 로 포트를 수정해봤다.

```
server.port = 8081
application.name = demo
```

## ✏️ 두번째 시도 (실패)

나와 에러코드와 문제가 흡사한 사람의 게시물을 찾았다.

intellij 무료버전의 경우 발생되는 문제라며 톰캣이 정상 작동되지 않아서 그렇다고한다.

build.gradle 에서 starter - tomcat 을 삭제하면 된다고 하는데 난 해당 코드가 존재하지 않았다..

## ✏️ 세번째 시도 (실패)

계속해서 구글링을 해보던중 인프런에 나와 같은 문제가 있는 사람을 찾았다.

무료버전에서 생기는 문제라고 하는데 gradle 을 intellij 에서 다시 gradle 로 바꾸면 된다고 했다.

시도해봤지만 작동이 안되서 다시 intellij 로 바꿔서 실행해봤는데 없던 에러가 하나 더 생겨버렸다..

아무것도 안만지고 원상복귀 했는데 왜그런거지 -.-…

- 새로 생긴 에러

```
Failed to initialize JPA EntityManagerFactory: Unable to create requested service

Exception encountered during context initialization - cancelling refresh attempt
```

## ✏️ 네번째 시도 (실패)

원인을 모르겠어서 복습용으로 만들어두었던 비슷한 다른 프로젝트를 실행해봤다.

어제까지 잘 되던 프로젝트가 똑같은 error 코드를 뱉으며 작동이 멈췄다.

혹시몰라 gradle 에서 gradle 로 바꿔주었더니 여기서는 실행이 됬지만

속도가 엄청 느려졌다…..

뭔가 다른사람들도 잘 되다가 안되는거 보면 체험판 이 종료되어서 그런건가 ? 싶기도하다..

다시 진행중인 프로젝트로 돌아와 gradle 로 바꿔봤는데 여기서는 계속해서 에러가 난다 ㅜㅜ

gradle 문제말고도 다른 문제가 더 있는게 분명하다…