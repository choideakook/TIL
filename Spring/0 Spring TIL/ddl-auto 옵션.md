# ddl-auto 옵션

## ✏️ ddl-auto 옵션의 종류

- create
    - 기존 Table 을 전부 삭제 후 다시 생성
    - DROP + CREATE
- create-drop
    - 결과적으로는 create 와 같으나 Table DROP 을 종료시점에 한다.
- update
    - 변경분만 반경
    - 윤영 DB 에서는 사용하면 안됨
- validate
    - entity 와 table 이 정상적으로 mapping 되었는지만 확인
- none
    - 사용하지않음
    - 사실상 없는 값이지만 관례상 none 이라고 표시함

## ✏️ 주의할 점

- 운영 장비에서는 절대로 create , create-drop 을 사용해서는 안된다.
    - 클라이언트들이 저장한 DB 가 삭제될 수 있음
- 개발 초기 단계는 create , update 를 사용하면 좋다.
- 테스트 서버는 update , validate 를 사용하면 좋다.
- 스테이징과 운영 서버는 validate 또는 none 을 사용하면 좋다.

<br>

❗️ 사실 로컬 환경을 제화한 나머지 서버에서는 최대한 직접 쿼리를 만들어 적용하는것이 가장 좋다.