# BadSqlGrammarException

Bad / Sql / Grammar / Exception

안좋은 / SQL / 문법 / 예외

SQL 문법이 틀렸을 때 나타나는 예외이다.

<br>

직역해봐도 알 수 있듯이 굳이 따로 정리하지 않아도 괜찮을 정도로 원인 파악이 쉬운 예외이지만 문법이 정확한 경우에도 발생할 수 있어서 정리해봤다.

<br>

### 📍 발단

JDBC Template 을 공부하던중 Application 을 실행하니 문법 예외가 발생했다.

에러메세지를 보면 친절하게 어느부분이 문제인지, 또 그 부분에서 어떤 단어가 어떻게 문제인지 친절하게 안내를 해준다.

나의 경우는 Item 이라는 Table 에 data 를 insert 할때 예외가 발생했는데
Item Table 을 찾을 수 없다는 메시지가 있었고,
DB 에서도, SQL 문에서도 문제가 없고 오타도 없어서 맨붕이 왔다.
<br>

아무리 찾아봐도 잘못된 부분을 찾지 못해서 프로잭트를 새로 생성해 다시만들어봤지만
새로 만든 프로젝트는 정상으로 작동하고 이전 프로젝트는 같은 예외가 발생했다.
<br>

### 📍 문제 해결

알고보니 Repository 계층에 Repository 어노테이션을 넣지 않아서 발생한 문제였다.

Config 가 Repository 를 인식할 수 없었고 당연히 Table 을 찾을 수 없어 문법 예외가 발생했던 것이다.

<br>

이런 이유로 BadSqlGrammarException 가 발생한 것이 이번이 처음이였고 어노테이션을 빠트려 발생한 예외라고 생각을 꿈에도 못해서 멀쩡한곳에서 문제를 찾다보니 발생한 해프닝이였다.

<br>

앞으로는 문제점을 못찾겠으면 좀더 폭넓고 원인에 대해서 문제점을 파악하는 습관을 가져야되겠다.