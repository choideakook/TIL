# README

## ✏️ API 개발 기본

### API

Application Programming Iterface

어플리케이션 프로그램 간에 데이터를 주고 받는 방법을 뜻한다.

- [🔗 API 가 필요한 이유](https://youtu.be/irPmSB-AB-Q)
- [🔗 API test 의 필요성](https://youtu.be/3c7L3fFUj-4)

<br>

## ✏️ API 개발 환경 세팅하기

project 기능들을 API 방식으로 바꾸기 위해 간단한 세팅이 필요하다.

### 📍 Post man 설치

[🔗 Post Man link](https://www.postman.com/downloads/)

포스트맨은 API 를 테스트 할 수 있는 플랫폼이다.

[🔗 Post man 사용법](https://inpa.tistory.com/entry/POSTMAN-💽-포스트맨-사용법-API-테스트-자동화-고급-활용까지)

[🔗 Post man 사용법](https://ssimplay.tistory.com/718)

<br>

### 📍 API package 생성하기

템플릿 엔진 스타일의 컨트롤러와 API 스타일의 컨트롤러의 package 를 분리하기 위해 API package 를 생성한다.

- 분리하는 이유
    - 공통으로 예외처리같은 작업을 할 때 구성단위, 패키지 단위로 공통처리가 된다.
    - 이 둘은 공통처리 할 요소가 너무 다르다.