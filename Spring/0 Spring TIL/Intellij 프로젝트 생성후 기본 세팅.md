# Intellij 프로젝트 생성후 세팅

## ✏️ Gradle 변경

Intellij 로 변경

<img width="500" alt="1" src="https://user-images.githubusercontent.com/115536240/211440307-55c3dec8-3fd5-45ff-b0e2-fa0e2cd3edd6.png">

## ✏️ Lombok 세팅

Preference → plugin → lombok 최신버전인지 확인

<img width="500" alt="2" src="https://user-images.githubusercontent.com/115536240/211440310-90dd8f58-4629-4ac5-b3ed-3ffaa43f7bfc.png">

enable annotation processing 에 체크
  - 롬복 같은 외부 라이브러리가 컴파일 시 문제없이 작동하도록 해주는 설정

build.gradel 의 dependencies 추가
  - 테스트에서 롬복을 사용할 수 있게된다.
```
//테스트에서 lombok 사용
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
```

## ✏️ git 자동 staging

git → Enable staging area 에 체크

<img width="500" alt="3" src="https://user-images.githubusercontent.com/115536240/211440314-25ab601c-53de-49de-9386-3e9878c3ba8b.png">

confirmation → when files are created → Add silently ( 자동으로 add 하기) 에 체크

- 앞으로 생성되는 file 들은 자동으로 add 됨

터미널에서 git repository 에 push 해주면 기존 파일들이 repository 에 업로드되고 앞으로의 파일들도 자동 업로드 된다.
