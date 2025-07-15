# 📦 ResponseEntity<> 설명 및 주요 메서드 정리

<div style="margin-top:80px;"></div>

## 🔹 ResponseEntity란?

- `ResponseEntity<T>`는 Spring Framework에서 HTTP 응답을 **직접 제어**할 수 있도록 제공하는 클래스이다.
- 일반적으로 REST API에서 **HTTP 상태 코드, 응답 헤더, 본문(body)**을 커스터마이징해서 반환할 때 사용한다.
- 제네릭 `<T>`는 응답 본문의 타입이다.

---

<div style="margin-top:80px;"></div>

## 🧱 기본 구조

```java
ResponseEntity<T>
````

* `T` : 반환할 데이터 타입 (예: `String`, `Member`, `List<User>` 등)

---


<div style="margin-top:80px;"></div>

## ✅ 사용 목적

| 기능         | 설명                                                                         |
| ---------- | -------------------------------------------------------------------------- |
| 상태 코드 설정   | 예: `200 OK`, `201 Created`, `404 Not Found`, `500 Internal Server Error` 등 |
| 응답 바디 설정   | 반환할 JSON 객체 또는 메시지를 body에 담아 보낼 수 있음                                       |
| 헤더 설정 가능   | 커스텀 HTTP 헤더 추가 가능 (예: Location, Authorization 등)                           |
| 조건별 응답 다양화 | 로직 흐름에 따라 상황에 맞는 HTTP 응답 반환 가능                                             |

---

<div style="margin-top:80px;"></div>

## ✨ 주요 정적 메서드 (ResponseEntity 팩토리 메서드)

| 메서드                  | 설명                                |
| -------------------- | --------------------------------- |
| `ok()`               | `200 OK` 상태 코드 반환. 본문 없이 응답       |
| `ok(body)`           | `200 OK` + 응답 본문 설정               |
| `created(URI)`       | `201 Created` 상태 + Location 헤더 설정 |
| `badRequest()`       | `400 Bad Request` 응답              |
| `status(HttpStatus)` | 임의의 상태 코드로 응답 생성                  |
| `notFound()`         | `404 Not Found` 응답 (Spring 5 이상)  |
| `noContent()`        | `204 No Content` 응답 (본문 없이 성공 처리) |

---

<div style="margin-top:80px;"></div>

## 🧪 사용 예시

### 1. 성공 응답 + JSON 객체 반환

```java
@GetMapping("/{id}")
public ResponseEntity<Member> getMember(@PathVariable Long id) {
    Member member = memberService.findById(id);
    return ResponseEntity.ok(member); // 200 OK + body
}
```

---

### 2. 데이터 없음 → 404 응답

```java
@GetMapping("/{id}")
public ResponseEntity<?> getMember(@PathVariable Long id) {
    Member member = memberService.findById(id);
    if (member == null) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                             .body("해당 사용자를 찾을 수 없습니다.");
    }
    return ResponseEntity.ok(member);
}
```

---

### 3. 생성 응답 + Location 헤더 포함

```java
@PostMapping
public ResponseEntity<Void> createUser(@RequestBody User user) {
    userService.save(user);
    URI uri = URI.create("/users/" + user.getId());
    return ResponseEntity.created(uri).build(); // 201 Created
}
```

---

### 4. 커스텀 상태 + 헤더 설정

```java
@GetMapping("/custom")
public ResponseEntity<String> customHeader() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("X-Custom-Header", "승현님-환영합니다");

    return ResponseEntity.status(HttpStatus.ACCEPTED)
                         .headers(headers)
                         .body("커스텀 응답입니다.");
}
```

---

<div style="margin-top:80px;"></div>

## 📌 요약

* `ResponseEntity`는 **상태 코드, 본문, 헤더를 유연하게 설정**할 수 있는 클래스이다.
* `return` 타입으로 설정하면 Spring이 자동으로 HTTP 응답을 구성한다.
* REST API에서 실무적으로 자주 사용되며, **정적 메서드를 활용하면 코드가 깔끔해진다.**


<div style="margin-top:80px;"></div>




## Response.status 상수정리

### Response.status(HttpStatus.상수명)

| 구분          | 상태 코드 | 상수명                     | 의미                |
| ----------- | ----- | ----------------------- | ----------------- |
| ✅ 성공        | `200` | `OK`                    | 요청 성공 (일반적인 응답)   |
|             | `201` | `CREATED`               | 리소스 생성됨           |
|             | `202` | `ACCEPTED`              | 요청 수락됨 (비동기 처리 등) |
|             | `204` | `NO_CONTENT`            | 응답 본문 없음          |
| 🚫 클라이언트 오류 | `400` | `BAD_REQUEST`           | 잘못된 요청            |
|             | `401` | `UNAUTHORIZED`          | 인증 실패 (로그인 필요)    |
|             | `403` | `FORBIDDEN`             | 인가 실패 (접근 권한 없음)  |
|             | `404` | `NOT_FOUND`             | 요청 리소스 없음         |
|             | `405` | `METHOD_NOT_ALLOWED`    | 허용되지 않은 HTTP 메서드  |
| 💥 서버 오류    | `500` | `INTERNAL_SERVER_ERROR` | 서버 내부 오류          |
|             | `502` | `BAD_GATEWAY`           | 잘못된 게이트웨이 응답      |
|             | `503` | `SERVICE_UNAVAILABLE`   | 서비스 불가 또는 다운      |


* 위 표는 Spring에서 정의한 HTTP 상태 코드(enum) 들이다. HttpStatus. 뒤에 붙이면 된다. 직접 번호를 적는것보다 가독성이 좋고 안정적