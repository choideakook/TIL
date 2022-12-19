# 로그인 화면 만들기

## ✏️ 기획

### 1. 홈 페이지

- 페이지 제목
- 아이디와 비밀번호 입력 필드
- 로그인 버튼
- 회원가입 버튼

### 2. 회원가입 페이지

- 사용자의 개인 정보를 입력하는 필드
    - 사용자 이름
    - 사용자 아이디
    - 사용자 패스웨드
    - 패스워드 확인
- 회원가입 버튼

### 3. 로그인 페이지

- 사용자의 이름 이 출력
- 로그 아웃 버튼 (메인페이지로 돌아감)

---

## ✏️ 설계

### 1. Controller

- homeController
    - 메인 페이지의 html 로 연결
    - post mapping 방식으로 회원가입과 로그인 페이지로 이동 할 수 있음
- memberForm
    - 사용자가 입렵한 값을 html 에서 가져와 Controller 로 전달해줌
- memberController
    - Login
        - 사용자가 입력한 id 와 pw 정보가 db 의 정보와 일치할 경우 loginForm.html 로 return 해줌
        - 일치하는 정보가 없을경우 message 창이 팝업됨
    - Create
        - 사용자가 입력한 이름 , user id , pw 를 입력한 후 회원 가입 버튼을 누르면 고유한 id 번호와 함께 db 에 저장됨
        - pw 와 pw 확인이 일치하지 않을경우 message 창이 팝업됨
        - 사용자가 입력하 user id 가 이미 db 에 있을경우 message 창이 팝업됨

### 2. Repository

- save
    - 정보를 DB 에 저장함
- findById / findByPw / findByName
    - DB 에서 해당 정보를 가져옴
- findAll
    - DB 에 저장된 모든 정보를 가져옴

### 3. Service

- join
    - user 아이디가 이미 db 에 있을경우 message 창이 팝업됨
    - 사용자가 입력한 정보를 save 를 이용해 DB 에 저장함
- findMemberName
    - 사용자의 name 을 가져옴