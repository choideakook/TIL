# IllegalStateException

Illegal / State / Exception

불법 / 상태 / 예외

직역하면 무슨뜻인지 잘 모르겠지만 객체가 method 호출을 할 수 없을 때 발생하는 오류라고 한다.

## ✏️ 발단

인프런 강의를 보면서 apllication.yml 에 환경설정을 해주고 db 연결 확인을 위한 test 를 실행하니 해당 에러가 발생했다.

에러 text 와 정황상 db 연결이 문제인것같다.

<br>

## ✏️ 원인

예전 강의를 들으며 h2 의 데이터 베이스를 생성한적이 있는데 데이터베이스가 이미 생성이되어있는데 새로운 db 생성 없이 강제로 수정하려고 해서 발생된 문제인것같다.

<br>

## ✏️ 해결

- 사용자 홈으로 디렉토리를 이동해 파일 리스트에서 h2 관련 파일을 확인한다.
    - 확인해보니 예전에 만들어놓았던 test.mv.db 가 있었다.

```yaml
cd
ll -arlth
```

- 이 파일을 복사해서 새로운 db를 생성해줬다.

```yaml
cp test.mv.db jpashop.mv.db
```

❗️ 기존에 db 파일을 삭제해버리면 정상적으로 작동되지 않으니 주의

<br>

- 콘솔을 실행한 후 JDBC url 에 변경된 url입력

```yaml
jdbc:h2:tcp://localhost/~/jpashop
```