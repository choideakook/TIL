# 일단 배우는 HTML/CSS

## ✏️ 프로젝트 목표

- 이력서 만들기

<img width="430" alt="1" src="https://user-images.githubusercontent.com/115536240/220220987-fc363f67-f1cd-464a-bf0e-b803f87d2991.png">

## ✏️ STEP 1

### 📍 HTML 과 인사하기

- html 과 CSS 를 사용하는 것은 문서를 만드는 작업이라고 생각하면된다.
- 문서를 만들기 위해서 다양한 플랫폼이 존재하지만 다른 프로그래밍 언어들과 호환이 어렵다.
- html 과 Css 로 작업한 문서는 프로그래밍 언어들과 호환성이 매우좋다는 장점을 갖고있다.

### 📍 HTML TAG

- Html 문서 내에서 컨텐츠들을 관리하기 위한 개념
- 태그를 사용해 HTML 의 수 많은 작업을 수행할 수 있다.

## ✏️ STEP 2

### 📍 문서의 골격

- head 태그와 body 태그를 이용해 문서으 골격을 만들었다.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>김멋사의 이력서</title>
    </head>
    <body>
        <h1>김멋사</h1>
        <p>HTML/CSS</p>
    </body>
</html>
```

## ✏️ STEP 3 - CSS 와 인사하기

### 📍 footer

- page 의 하단부에 위치한 패키지
- 회사의 정보, 회사의 주소, 저작권 명시 등 을 기록한다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>김멋사의 이력서</title>
  </head>
  <body>
    <h1>김멋사</h1>
    <p>HTML/CSS 개발자</p>
    <footer>copyright CODE LION. All rights reserved.</footer>
  </body>
</html>
```

### 📍 CSS 로 footer 꾸미기

- html 파일 내에 CSS 코드를 작성할 수도 있지만 가독성과 유지보수성이 낮아지기 때문에 CSS 는 별도의 파일에서 관리하는것이 좋다.
- head 태그에 CSS 를 링크 태그를 통해 명시해 놓아야 별도 CSS 파일을 적용할 수 있다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>김멋사의 이력서</title>

    <!-- head 태그 내에 CSS 파일을 link 해 주었다. -->
    <link rel="stylesheet" href="codelion.css">

  </head>
  <body>
    <h1>김멋사</h1>
    <p>HTML/CSS 개발자</p>
    <footer>copyright CODE LION. All rights reserved.</footer>
  </body>
</html>
```

### 📍 CSS 코드 작성

- footer 에 대한 CSS 코드를 작성했다.
- HTML 파일 내의 모든 footer 태그는 이 CSS 의 코드가 적용된다.

```css
footer {
    text-align: center;
    background-color: black;
    color: white;
    font-size: 12px;
}
```

## ✏️ STEP 4 - 가독성 챙기기

### 📍 같은 태그를 서로다른 CSS 로 적용하기 - Class 사용

- 아래의 코드에서 p 태그를 CSS 로 적용시키면 HTML 내의 모든 p 태그가 하나의 CSS 로 똑같은 영향을 받게 된다.

```html
    <head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="codelion.css">
    </head>
    <body>
        <p>내 이름은 김멋사</p>
        <p>코드라이언으로 코딩 배웠지</p>
        <p>반갑습니다</p>
    </body>
```

- Class 를 사용하면 같은 태그안에서 다른 CSS 를 적용할 수 있다.
    - 첫번째와 두번째 p 태그 , 세번째 p 태그를 각각 묶어주었다.
    - CSS 에서 각각의 Class 를 대상으로 같은 태그도 각각 관리할 수 있다.

```css
<head>
        <meta charset="UTF-8">
        <link rel="stylesheet" href="codelion.css">
    </head>
    <body>
        <p class="big-font">내 이름은 김멋사</p>
        <p class="small-font">코드라이언으로 코딩 배웠지</p>
        <p class="small-font">반갑습니다</p>
    </body>
```

- css 에서의 코드 작성
    - Class 이름 앞에 . 을 꼭 붙여주어야 한다.

```css
p {
    font-size: 30px;
}

.big-font {
    font-size: 40px;
}

.small-font {
    font-size: 15px;
}
```

## ✏️ STEP 5

### 📍 컬러 관리하기

- 컬러를 설정하기 위해서 직접 컬러를 눈으로 확인하면 좋겠지만 html 내에서 컬러를 확인할 수는 없다.
    - 외부의 툴을 사용해 문제를 해결할 수 있다.
    - html color code 라고 검색하면 적당한 툴을 사용할 수 있다.
    - 컬러 코드를 확인해서 적용하면 쉽게 원하는 컬러를 사용할 수 있다.

```css

footer {
    text-align: center;
    background-color: #1e1e1e; // 컬러코드를 사용해 색을 변경해줌
    color: #919191;
    font-size: 12px;
}
```

### 📍 div 사용해서 테두리 만들기

- div (division)
    - 여러 태그들을 하나로 묶어서 관리할 수 있는 태그
    - h1 과, p 를 묶어서 div 태그로 관리한다.
    - div 의 class 는 mainbox 로 해주었다.

```html
    <body>
        <div class="mainbox">
            <h1>김멋사</h1>
            <p>HTML/CSS 개발자</p>
        </div>
        <footer>copyright CODE LION. All rights reserved.</footer>
    </body>
```

- 테두리 CSS
    - border : 두께 방식 색깔;
    - 위의 예제 순으로 입력하면 테두리가 적용된다.

```css
footer {
    text-align: center;
    background-color: #1e1e1e;
    color: #919191;
    font-size: 12px;
}

.mainbox {
    border: 1px solid #ebebeb;
    width: 610px;
    // text 를 가운데 정렬
    text-align: center;
    // tag 자체 (박스) 를 가운데 정렬 (테두리로 확인할 수 있다.)
    margin-left: auto;
    margin-right: auto;
}
```

## ✏️ STEP 6

### 📍 박스의 구성

<img width="461" alt="2" src="https://user-images.githubusercontent.com/115536240/220220995-322346d3-21f7-4d48-b98e-cfdba6d68e2c.png">

### 📍 Box 의 각 명칭을 사용해 CSS 적용하기

- 각 값을 조정해 box 를 원하는데로 조정할 수 있다.

```css
.box1 {
    background-color: skyblue;
    width: 60px;
    height: 60px;
    border: 5px solid black;
    padding: 20px;
    margin: 20px;
}

.box2 {
    background-color: violet;
    width: 100px;
    height: 100px;
    border: 5px solid purple;
}
```

<img width="550" alt="3" src="https://user-images.githubusercontent.com/115536240/220220996-19fd129b-4691-447e-96d8-d2ea01c74e03.png">

### 📍 프로젝트에 적용 하기

- 위의 개념을 이용해 만들고 있는 이력서 제목에 적용하면 된다.

```html
<body>
    <div class="mainbox">
      <h1>김멋사</h1>
      <p class="name-text">HTML/CSS 개발자</p>
    </div>
    <footer>
      <p>Copyright CODE LION All rights reserved.</p>
    </footer>
  </body>
```

```css
.name-text {                                  
    font-size: 17px;                         
    color: #7c7c7c;
    font-weight: bold;
}

.mainbox {
    width: 610px;
    padding: 30px;
    margin: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid #ebebeb;
}

footer {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    font-size: 12px;
    color: #919191;
}
```

## ✏️ STEP 7 - 그림자 표현하기

### 📍 box-shadow

- box-shadow 를 사용해 box  의 그림자를 표현할 수 있다.
    - box-shadow: {1} {2} {3} {4} rgba({5},{6},{7},{8});
    1. 가로 축으로 뻣어나가는 그림자의 크기
        - 양수면 오른쪽, 음수면 왼쪽으로 뻣어나간다.
    2. 새로 축으로 뻣어나가는 그림자의 크기
    3. 그림자의 흐림 정도 (블러값)
    4. 그림자의 퍼짐 정도 (스프레드)
    
    5~8 그림자의 색 (0~255 까지의 숫자로 강도를 조절할 수 있다.)
    
    r : red
    
    g : green
    
    b : blue
    
    a : 투명도 ( 투명도는 0~1 까지의 숫자로 강도를 조절 한다.)
    

```css
box-shadow: 0 1px 20px 0 rgba(0,0,0,0.1);
```

- main 박스에 그림자를 적용한다.

```css
.name-text {                                  
    font-size: 17px;                         
    color: #7c7c7c;
    font-weight: bold;
}

.mainbox {
    width: 610px;
    padding: 30px;
    margin: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid #ebebeb;
    box-shadow: 0 1px 20px 0 rgba(0,0,0,0.1);
}

footer {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    font-size: 12px;
    color: #919191;
}
```

## ✏️ STEP 8 - 구글 웹 폰트 사용하기

- 아래의 문법으로 html 문서의 폰트를 변경할 수 있다.

```css
@import url( 폰트 주소 )
```

- 원하는 폰트를 google 에서 검색해서 html 내의 모든 폰트에 적용시켰다.
    - Montserrat 라는 폰트를 사용했다.

```css
@import url('https://fonts.googleapis.com/css?family=Montserrat:100,200,300,400,500,600,700,800&display=swap');

* {
    font-family: 'Montserrat'
}
```

## ✏️ STEP 9 - CSS 기본 세팅 마무리 하기

```css
@import url('https://fonts.googleapis.com/css?family=Montserrat:100,200,300,400,500,600,700,800&display=swap');

* {
    font-family: 'Montserrat';
}

body,h1,h2 {
    margin: 0px;
    padding: 0px;
}

body {
    min-width: fit-content;
}

.name-text {                                  
    font-size: 17px;                         
    color: #7c7c7c;
    font-weight: bold;
}

.mainbox {
    width: 610px;
    padding: 30px;
    margin: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid #ebebeb;
    box-shadow: 0 1px 20px 0 rgba(0, 0, 0, 0.1)
}

footer {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    font-size: 12px;
    color: #919191;
}
```

### 📍 Title오른쪽 정렬 하기

- 계획했던 것 처럼 이름과 1줄 소개를 오른쪽 정렬 시키기 위해 이 두가지 태그를 하나의 div 태그로 묶어준다.
    - Class 이름은 title-box 로 작성했다.

```html
   <div class="title-box">
        <h1>김멋사</h1>
        <p class="name-text">HTML/CSS 개발자</p>
    </div>
```

- CSS 에서 title-box 를 오른쪽 정렬 시켜준다.

```css
.title-box {
    text-align: right;
}
```

## ✏️ STEP 10

### 📍 About me 작성하기

- About me 의 내용을 담을 p 태그에 일단 로렘 입숨을 붙여넣기 해준다.
    - 로렘 입숨은 html 작업도중 디자인 적인 부분을 확인하기 위해서 임의의 본문 내용을 아무렇게나 입력하는 것을 뜻한다.
    - google 에 검색하면 다양한 로렘 입숨을 구할 수 있다.

```css
<body>
  <div class="mainbox">
    <h1>김멋사</h1>
    <p class="name-text">HTML/CSS 개발자</p>
    <section>
      <h2>ABOUT ME</h2>
      <p class="about-me-text">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
      </p>
    </section>
  </div>
  <footer>
      <p>Copyright CODE LION All rights reserved. </p>
  </footer>
</body>
```

### 📍 section

- About me 의 내용은 section 태그 안에 작성되었다.
    - section 태그는 div 와 기능이 동일하지만,
    모든 패키징을 div 로 만들경우 가독성이 매우 좋지못해 div 와 구별하기 위한 태그이다.
    - section 외에 같은 기능을 제공하는 article 태그도 존재한다.

### 📍 CSS 적용

- 틀이 완성되었으므로 디테일한 위치 조정과 폰트 설정은 CSS 에서 작업해 준다.

```css
@import url('https://fonts.googleapis.com/css?family=Montserrat:100,200,300,400,500,600,700,800&display=swap');

* {
    font-family: 'Montserrat';
}

body,h1,h2 {
    margin:0px;
    padding:0px;
}

body {
    min-width: fit-content;
}

h1 {
    font-size: 36px;
    font-weight: bold;
    font-style: italic;
}

h2 {
    font-size: 20px;
    font-weight: lighter;
    color: #282828;
    border-bottom: 1px solid #ebebeb;
}

.mainbox {
    width: 610px;
    padding: 30px;
    margin: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid #ebebeb;
    box-shadow: 0 1px 20px 0 rgba(0, 0, 0, 0.1);
}

footer {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    font-size: 12px;
    color: #919191;
}

section {
    margin-bottom: 24px;
}

.name-text {
    font-size: 16px;
    color: #7c7c7c;
    font-weight: bold;
}

.about-me-text {
    font-size: 10px;
    line-height: 16px;
}
```

## ✏️ STEP 11

### 📍 Experience 작성하기

- 우리가 계획한 Experience 의 내용은 왼쪽에 경력이 있고, 오른쪽에 경력의 날짜가 있어야 한다.
    - 만약 text-align 을 사용하면 두가지 내용을 하나의 줄에 작성할 수 없고
    줄이 각각 바뀌어서 작성되어버린다.
- float 로 문제해결
    - float 문은 다른 태그에 영향을 받지 않고 현재 위치한 container 내에서 지정된 위치로 고정되어진다.
    - float 문을 사용하면 두가지 내용을 같은 줄에 왼쪽, 오른쪽 정렬을 시킬 수 있다.

```css
.title-text {
    font-size:11px;
    font-weight: bold;
    color: #282828;
    float: left;
}

.year-text{
    font-size:11px;
    font-weight: bold;
    color: #282828;
    float: right;
}
```

## ✏️ STEP12

### 📍 뗏목 띄우기

- 앞서 Step 11 에서 사용한 float 문을 사용한 뒤 새로운 태그의 text 를 작성하면 float 가 설정 되어있는 태그와 겹침 현상이 발생하게된다.
    - float 문의 성격 때문에 발생한 문제이다.
- 문제를 해결하기 위해 각각의 영역을 확실하게 갈라줄 필요가 있다.
    - float 문이 사용된 2개의 p 태그를 하나의 div 태그로 묶어준다.

```html
<section>
      <h2>EXPERIENCE</h2>
      <div class="float-wrap">
        <p class="title-text">Awesome Programming Company</p>
        <p class="year-text">2020 - Now</p>
      </div>
      <p class="desc-text">Front-End Web Developer</p>
    </section>
```

- float-wrap 클래스를 CSS 에서 꾸며준다.
    - overflow: hidden; 를 사용하면 float 로 띄워져있는 태그들을 모두 묶어주고,
    다음에 올 태그들이 float 와 겹치지 않게 막아준다.

```css
.float-wrap {
    overflow: hidden;
}
```

- 나머지 CSS 도 보기좋게 꾸며준다.

```css
.float-wrap {
    overflow: hidden;
}

.desc-text {
    font-size: 9px;
}

.desc-subtext {
    font-size: 9px;
    color: #282828;
    padding-left: 16px;
}
```

- 이제 Experience 의 다른 경력도 추가해준다.
    - 방금 만든 form  을 복사 붙여넣기 해서 내용만 변경해주면 똑같이 CSS 가 적용된다.

```html
    <section>
      <h2>EXPERIENCE</h2>
    <div class="float-wrap">
        <p class="title-text">Awesome Programming Company</p>
        <p class="year-text">2020 - Now</p>
      </div>
      <p class="desc-text">Front-End Web Developer</p>
      <p class="desc-subtext">HTML/CSS,JS,React,...</p>

      <div class="float-wrap">
        <p class="title-text">Ministry of Health</p>
        <p class="year-text">2015 - 2018</p>
      </div>
      <p class="desc-text">UI/UX Designer</p>
      <p class="desc-subtext">Web design</p>

      <div class="float-wrap">
        <p class="title-text">Freelance Work</p>
        <p class="year-text">2011 - 2015</p>
      </div>
      <p class="desc-text">Graphic Designer</p>
      <p class="desc-subtext">Graphic Design, Editorial Design</p>
    </section>
```

## ✏️ STEP 13

### 📍 이미지 아이콘 넣기

- 먼저 이미지 아이콘을 하나의 div 안에 img 태그를 이용해 작성해 준다.

```html
    <div class="sns-wrap">
        <img class="sns-img" src="images/facebook.png">
        <img class="sns-img" src="images/twitter.png">
        <img class="sns-img" src="images/linked-in.png">
        <img class="sns-img" src="images/insta.png">
    </div>
```

- CSS 를 이용해 sns-wrap 를 계획했던 것 처럼 오른쪽으로 정렬시켜준다.

```css
.sns-wrap {
    text-align: right;
}
```

- 이렇게 만들어진 아이콘은 단순한 image 이기 때문에 클릭해도 아무 기능을 할 수 없다.
    - 아이콘을 클릭할 때 해당 page 로 이동시키기 위해서는 a 태그를 사용해 이미지에 링크를 생성해 주어야 한다.

```html
<a href="http://facebook.com"><img class="sns-img" src="images/facebook.png"></a>
```

## ✏️ STEP 14

### 📍 정리

- HTML

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>김멋사의 이력서</title>
  <link rel="stylesheet" href="codelion.css">
</head>
<body>
  <div class="mainbox">
    <div class="title-box">
      <h1>김멋사</h1>
      <p class="name-text">HTML/CSS 개발자</p>
    </div>
    <section>
      <h2>ABOUT ME</h2>
      <p class="about-me-text">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
      </p>
    </section>
    <section>
        <h2>EXPERIENCE</h2>
        <div class="float-wrap">
          <p class="title-text">Awesome Programming Company</p>
          <p class="year-text">2020 - Now</p>
        </div>
        <p class="desc-text">Front-End Web Developer</p>
        <p class="desc-subtext">HTML/CSS, JS, React, ...</p>
        <div class="float-wrap">
          <p class="title-text">Ministry of Health</p>
          <p class="year-text">2015 - 2018</p>
        </div>
        <p class="desc-text">UI/UX Designer</p>
        <p class="desc-subtext">Web design</p>
        <div class="float-wrap">
            <p class="title-text">Freelance Work</p>
            <p class="year-text">2011 - 2015</p>
        </div>
        <p class="desc-text">Graphic Designer</p>
        <p class="desc-subtext">Graphic Design, Editorial Design</p>
      </section>

      <section>
        <h2>ACTIVITIES</h2>
        <div class="float-wrap">
          <p class="title-text">Front-End Web Developer Forum Volunteer</p>
          <p class="year-text">2019 - 2020</p>
        </div>
        <p class="desc-text">Application Page Development</p>
        <p class="desc-subtext">Lorem ipsum dolor sit amet.</p>
        <div class="float-wrap">
          <p class="title-text">LIKELION SEOUL</p>
          <p class="year-text">2017 - 2018</p>
        </div>
        <p class="desc-text">Teacher in Mutsa University</p>
        <p class="desc-subtext">Lorem ipsum dolor sit amet.</p>
        <div class="float-wrap">
          <p class="title-text">Open Source Committer</p>
          <p class="year-text">2011 - 2013</p>
        </div>
        <p class="desc-text">Angular JS</p>
        <p class="desc-subtext">Lorem ipsum dolor sit amet.</p>
    </section>

    <section>
        <h2>EDUCATION</h2>
        <div class="float-wrap">
            <p class="title-text">Mutsa University</p>
            <p class="year-text">2008 - 2012</p>
        </div>
        <p class="desc-text">Computer Science and Engineering</p>
        <div class="float-wrap">
            <p class="title-text">Mutsa High school</p>
            <p class="year-text">2006 - 2008</p>
        </div>
        <p class="desc-text">Visual Communication Design</p>
        <div class="float-wrap">
            <p class="title-text">Online Programming Academy</p>
            <p class="year-text">2006 - 2007</p>
        </div>
        <p class="desc-text">Python Course</p>
    </section>

    <section>
      <h2>AWARDS</h2>
      <div class="float-wrap">
        <p class="title-text">LIKELION SEOUL</p>
        <p class="year-text">2018</p>
      </div>
      <p class="desc-text">Best Developer Award</p>
    </section>

    <div class="sns-wrap">
      <a href="http://facebook.com"><img class="sns-img" src="images/facebook.png"></a>
      <img class="sns-img" src="images/twitter.png">
      <img class="sns-img" src="images/linked-in.png">
      <img class="sns-img" src="images/insta.png">
    </div>
  </div>
  <footer>
      <p>Copyright CODE LION All rights reserved. </p>
  </footer>
</body>
</html>
```

- CSS

```css
@import url('https://fonts.googleapis.com/css?family=Montserrat:100,200,300,400,500,600,700,800&display=swap');

* {
    font-family: 'Montserrat';
}

body,h1,h2 {
    margin:0px;
    padding:0px;
}

body {
    min-width: fit-content;
}

h1 {
    font-size:36px;
    font-weight: bold;
    font-style: italic;
}

h2 {
    font-size:20px;
    color:#282828;
    font-weight: lighter;
    margin-bottom: 16px;
    border-bottom: 1px solid #ebebeb;
    padding-bottom: 5px;
}

.name-text {
    font-size:16px;
    color:#7c7c7c;
    font-weight: bold;
}

.about-me-text {
    font-size:10px;
    line-height: 16px;
}

.mainbox {
    width: 610px;
    padding: 30px;
    margin: 30px;
    margin-right: auto;
    margin-left: auto;
    border: 1px solid #ebebeb;
    box-shadow: 0 1px 20px 0 rgba(0, 0, 0, 0.1);
}

.title-box {
    text-align: right;
}

section {
    margin-bottom:24px;
}

.float-wrap {
    overflow: hidden;
}

.title-text {
    font-size:11px;
    font-weight: bold;
    color: #282828;
    float: left;
}

.year-text{
    font-size:11px;
    font-weight: bold;
    color: #282828;
    float: right;
}

.desc-text {
    font-size: 9px;
}

.desc-subtext {
    font-size: 9px;
    color:#282828;
    padding-left:16px;
}

.sns-img {
    width:12px;
    height:12px;
}

.sns-wrap {
    text-align:right;
}

footer {
    text-align: center;
    background-color: #1e1e1e;
    padding: 20px;
    font-size: 12px;
    color: #919191;
}
```
