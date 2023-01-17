# Mapping 의 종류와 기능

### 📍 @GetMapping

- url 을 가져오는 mapping
- 단순 web page 를 보여주거나 조회 처리를 할 때 사용됨

### 📍 @PostMapping

- web 에서 post 방식으로 보낸 url 을 가져오는 방식
- 보통 web 서비스 이용자가 입력한 정보를 백으로 보낼때 사용
    - DB 에 등록할 때 사용됨

## ✏️ @PutMapping

- 리소스를 갱신하고 생성하는 작업을 한다.
- CRUD에서 C,U작업을 한다.
- POST와 다르게 같은 명령을 내렸을 때 해당 데이터가 없다면 새로 생성하고 해당 데이터가 있으면 업데이트를 하기에 PUT요청이 여러번 실행되어도 해당 데이터는 같은 상태이기에 멱등하다.
- POST와 동일하게 데이터 조작을 하기에 안정성은 없다.
- Path Variable을 사용가능하다.
- Data Body를 사용 가능하다.

<br>

### 📍 **사용되는 Annotation의 종류**

- **@RestController**
    - 해당 annotation을 추가해주면 해당 class는 REST API를 처리하는 controller로 등록이 된다.
- **@RequestMapping(path)**
    - 리소스를 설정하는 코드이며 괄호안에 입력하는 값에 따라 URI가 localhost:8080/path로 설정된다.
- **@PutMapping(path)**
    - PUT API를 해당 URI로 mapping시켜준다.
- **@RequestBody**
    - Request body부분을 Parsing해준다.
- **@PathVariable**
    - 변화하는 구간에 사용하는 annotation이며 URL path를 parsing해준다.
- **@JsonProperty**
    - 지정한 변수에 대해 Snake case와 Camel case의 변수를 mapping해준다.
- **@JsonNaming**
    - 해당 Class 내에 있는 모든 변수에 대해 Snake case와 Camel case의 변수를 mapping해준다.