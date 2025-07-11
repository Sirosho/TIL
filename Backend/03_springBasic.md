
# 🌱 Spring MVC 정리

> 웹 요청이 들어올 때 어떤 흐름으로 처리되는가?  
> 어떤 어노테이션을 써야 하고, 뷰는 어떻게 렌더링되는가?  
> 그리고 실무에서는 어떻게 REST API를 구현하는가?

---

<div style="margin-top:80px;"></div>

## 🐽 요청과 DispatcherServlet

- 사용자의 요청은 **DispatcherServlet**이 가장 먼저 받는다.
- Dispatcher는 요청을 적절한 **@Controller**에 전달한다.
- 각 컨트롤러는 매핑된 URL에 따라 **핸들러 메서드**를 실행한다.
- 최종적으로 문자열을 반환하면, 뷰 리졸버가 `.jsp`로 포워딩한다.

<div style="margin-top:80px;"></div>

뷰 설정 예시:

```properties
spring.mvc.view.prefix = /WEB-INF/views/
spring.mvc.view.suffix = .jsp
````

텍스트 `"hello"`를 반환하면 → `/WEB-INF/views/hello.jsp`로 이동!




<div style="margin-top:80px;"></div>


## 🤩 컨트롤러 매핑의 기본

* `@Controller`: 이 클래스는 컨트롤러다! 라고 Spring에 등록
* `@RequestMapping`: 클래스/메서드 단위로 URL 경로를 지정
* `@GetMapping`, `@PostMapping`: 각각 GET, POST 요청 처리 전용

<div style="margin-top:80px;"></div>

```java
@GetMapping("/hello")
public String hello() {
    return "hello"; // hello.jsp로 포워딩
}
```

---


<div style="margin-top:80px;"></div>

## 🔁 요청 방식에 따른 처리

| 요청 방식  | 어노테이션            | 설명                        |
| ------ | ---------------- | ------------------------- |
| GET    | `@GetMapping`    | 브라우저 주소창, `<a>` 태그 등      |
| POST   | `@PostMapping`   | 폼 전송, fetch API 등         |
| PUT    | `@PutMapping`    | 자원의 전체 수정, fetch API에서 사용 |
| DELETE | `@DeleteMapping` | RESTful API에서 삭제 요청 처리    |


<div style="margin-top:30px;"></div>

* 브라우저 요청:

  * 주소창 → **GET**
  * `<form method="POST">` → **POST**
  * **PUT/DELETE는 form에서 직접 전송 불가**

* JavaScript 요청:

  * `fetch(url, { method: 'PUT', body: ... })`처럼 **PUT/DELETE도 자유롭게 전송 가능**

  <div style="margin-top:80px;"></div>



## 📡 HTTP 직접 호출

```bash
curl -X GET "http://localhost:9000/chap2-2/hello"
```

* 위 명령어로 컨트롤러 메서드 테스트 가능
* 실습 중 `@ResponseBody`를 붙이면 응답이 JSON 등 데이터 자체가 된다

---

<div style="margin-top:80px;"></div>


## 📃 가상 상품 저장소 - REST 실습

> 상품 정보를 메모리에 저장하고 REST API로 조회/등록/삭제해보자.

* 상품 저장: `Map<Long, Product>` 형태로 저장소 구성
* 시리얼 넘버는 `auto-increment` 전략으로 1씩 증가
* 상품 조회 시 `@RequestParam`이나 `@PathVariable`로 값 전달



```java
@GetMapping("/get")
public Product get(@RequestParam Long id) {
    return productMap.get(id);
}
```

---


<div style="margin-top:80px;"></div>

## 📦 DTO + Lombok으로 코드 간소화

* 상품 데이터는 DTO 클래스로 설계
* 필드: `id`, `name`, `price`, `category`
* `@Data`를 붙이면 lombok이 getter, setter, toString 등을 자동 생성

```java
@Data
public class Product {
    private Long id;
    private String name;
    private int price;
    private String category;
}
```

> 실무에서는 필요한 필드만 명시적으로 만들기도 한다


<div style="margin-top:80px;"></div>



## 🧐 마무리

* 컨트롤러는 사용자의 요청을 받아 처리하고, 결과를 뷰로 포워딩한다.
* JSP는 뷰 리졸버가 prefix/suffix 기반으로 렌더링한다.
* HTTP 메서드에 따라 다른 핸들러 메서드를 구성할 수 있다.
* 실습에서는 `curl`, `fetch`, `@ResponseBody`, `@PathVariable`, `Map` 기반 저장소 등을 적극 활용했다.

