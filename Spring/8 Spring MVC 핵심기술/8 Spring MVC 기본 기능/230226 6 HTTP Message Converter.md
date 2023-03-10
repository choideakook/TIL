# HTTP Message Converter

## βοΈΒ HTTP Message Converter

### πΒ Converter μ νμμ±

View ν¬νλ¦ΏμΌλ‘ HTML μ μμ±ν΄ μλ΅νλ κ²μ΄ μλ,

HTTP API λλ λ¨μ Text μ²λΌ message body λ₯Ό μ§μ  μ½κ±°λ μ°λ κ²½μ° 

HTTP message Converter λ₯Ό μ¬μ©νλ κ²μ΄ μ’λ€.

<br>

### π@ResponseBody μ μλ¦¬

[πΒ @ResponseBody](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20ν΅μ¬κΈ°μ /8%20Spring%20MVC%20κΈ°λ³Έ%20κΈ°λ₯/230226%202%20HTTP%20μμ²­%20-%20λ¨μ%20text.md)

[πΒ @View Resolver](https://github.com/choideakook/TIL/blob/main/Spring/8%20Spring%20MVC%20ν΅μ¬κΈ°μ /5%20MVC%20νλ μμν¬%20λ§λ€κΈ°/230215%202%20V3%20-%20Model%20μΆκ°.md)

1. λΈλΌμ°μ μμ api μμ²­μ΄ λ€μ΄μ΄
2. Spring Container μ method κ° url μ μν΄ νΈμΆλ¨
3. `@ResponseBody` λ `@RestController` κ° μ μΈλμ΄ μλ€λ©΄ `HTTP Message Converter` κ° μλλλ€.
    - κΈ°λ³Έ `@Controller` λ§ μ μΈλμ΄ μλ€λ©΄ `View Resolver` κ° μλλλ€.
4. `HTTP Message Converter` λ String λ°νκ°μ JSON λλ Text λ‘ λ³κ²½ν΄ μλ΅νλ€.
    - HTTP body μ λ΄μ©μ΄ μ§μ  λ°νλλ€.
    - κΈ°λ³Έ λ¬ΈμμΌκ²½μ° `StringHttpMessageConverter` λ‘ λ¨μ Text λ°ν
    - κ°μ²΄ μΌκ²½μ° `MappingJackson2HttpMessageConverter` λ‘ JSON λ°ν

<img width="551" alt="s882" src="https://user-images.githubusercontent.com/115536240/221516914-1f8f600b-5c16-4448-8da4-5fe4a1ea16eb.png">

<br>

### πΒ HTTP Message Converter μ κΈ°λ³Έ κ°λ

- HTTP μμ²­, μλ΅ λ λ€ μ¬μ©λλ€.
    - `canRead()` , `canWirte()` - λ©μμ§ μ»¨λ²ν°κ° ν΄λΉ Class, λ―Έλμ΄ νμμ μ§μνλμ§ μ²΄ν¬
        - λ―Έλμ΄ νμμ header μ Content-type μ μλ―Ένλ€.
        - Content-type μ νμΈν΄ μ»¨λ²νμ΄ κ°λ₯νμ§, μ΄λ€ ννλ‘ μ»¨λ²νμ ν΄μΌ νλμ§ νλ¨νλ€.
    - `read()` , `write()` - λ©μμ§ μ»¨λ²ν°λ₯Ό ν΅ν΄μ λ©μμ§λ₯Ό μ½κ³  μ°λ κΈ°λ₯

<br>

### πΒ μμλλ©΄ μ’μ message converter

- `ByteArrayHttpMessageConverter` : `byte[]` λ°μ΄ν°λ₯Ό μ²λ¦¬νλ€.
    - ν΄λμ€ νμ: `byte[]` , λ―Έλμ΄νμ: */* (μλ¬΄ νμμ΄λ μμ©κ°λ₯)
    - μμ²­ μ) `@RequestBody byte[] data`
    - μλ΅ μ) `@ResponseBody return byte[]`
        - μ°κΈ° λ―Έλμ΄νμ `application/octet-stream`

<br>

- `StringHttpMessageConverter` : String λ¬Έμλ‘ λ°μ΄ν°λ₯Ό μ²λ¦¬νλ€.
    - ν΄λμ€ νμ: String , λ―Έλμ΄νμ: */* (μλ¬΄ νμμ΄λ μμ©κ°λ₯)
    - μμ²­ μ) `@RequestBody String data`
    - μλ΅ μ) `@ResponseBody return "ok"`
        - μ°κΈ° λ―Έλμ΄νμ `text/plain`

<br>

- `MappingJackson2HttpMessageConverter` : `application/json`
    - ν΄λμ€ νμ: κ°μ²΄ λλ `HashMap` , λ―Έλμ΄νμ `application/json` κ΄λ ¨
    - μμ²­ μ) `@RequestBody HelloData data`
    - μλ΅ μ) `@ResponseBody return helloData`
        - μ°κΈ° λ―Έλμ΄νμ `application/json` κ΄λ ¨

<br>

### πΒ HTTP μμ²­ λ°μ΄ν° μ½κΈ° κ³Όμ 

1. HTTP μμ²­μ΄ μ€κ³ , Controller μμ @RequestBody , HttpEntity νλΌλ―Έν°λ₯Ό μ¬μ©νλ€.
2. λ©μμ§ μ»¨λ²ν°κ° λ©μμ§λ₯Ό μ½μ μ μλμ§ νμΈνκΈ° μν΄ `canRead()` λ₯Ό νΈμΆνλ€.
    - λμ Class νμμ μ§μνλμ§
    - HTTP μμ²­μ `Content-type λ―Έλμ΄ νμ` μ μ§μνλμ§
3. `canRea()` μ‘°κ±΄μ λ§μ‘±νλ©΄ `read()` λ₯Ό νΈμΆν΄μ κ°μ²΄λ₯Ό μμ±νκ³ , λ°ννλ€.

<br>

### πΒ HTTP μλ΅ λ°μ΄ν° μμ± κ³Όμ 

1. Controller μμ @ResponseBody, HttpEntity λ‘ κ°μ΄ λ°νλλ€.
2. λ©μμ§ μ»¨λ²ν°κ° λ©μμ§λ₯Ό μΈ μ μλμ§ νμΈνκΈ° μν΄ `canWrite()` λ₯Ό νΈμΆνλ€.
    - λμ Class νμμ μ§μνλμ§
    - HTTP μμ²­μ `Accept λ―Έλμ΄ νμ` μ μ§μνλμ§
3. `canWrite()` μ μ‘°κ±΄μ λ§μ‘±νλ©΄ `wirte()` λ₯Ό νΈμΆν΄ HTTP μλ΅ λ©μμ§λ₯Ό body μ μμ±νλ€.
