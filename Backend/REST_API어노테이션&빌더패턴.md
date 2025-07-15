# 📘 REST API 어노테이션 & 빌더 패턴 정리

<div style="margin-top:80px;"></div>

## 📌 1. Spring REST API 어노테이션 정리

Spring Framework 기반의 REST API에서 주로 사용되는 어노테이션들을 기능별로 구분하여 정리하였다.

<div style="margin-top:80px;"></div>

### 🔹 요청 처리 관련 어노테이션

| 어노테이션         | 설명                                      |
|--------------------|-------------------------------------------|
| `@GetMapping`      | GET 요청 처리. 주로 데이터 조회 시 사용한다. |
| `@PostMapping`     | POST 요청 처리. 주로 데이터 등록 시 사용한다. |
| `@PutMapping`      | PUT 요청 처리. 전체 데이터를 수정할 때 사용한다. |
| `@PatchMapping`    | PATCH 요청 처리. 일부 필드만 수정할 때 사용한다. |
| `@DeleteMapping`   | DELETE 요청 처리. 데이터 삭제에 사용한다. |

### 🔹 요청 파라미터 바인딩 관련 어노테이션

| 어노테이션           | 설명                                                  |
|----------------------|-------------------------------------------------------|
| `@RequestParam`      | 쿼리 스트링 파라미터를 바인딩한다 (예: `?name=kim`).   |
| `@PathVariable`      | URI 경로에 있는 값을 변수로 바인딩한다.               |
| `@RequestBody`       | JSON 데이터를 객체로 변환하여 바인딩한다.             |
| `@ModelAttribute`    | 폼 데이터를 객체에 바인딩한다.                        |
| `@RequestHeader`     | 요청 헤더 값을 바인딩한다.                            |
| `@CookieValue`       | 쿠키 값을 바인딩한다.                                 |

### 🔹 응답 처리 및 기타

| 어노테이션          | 설명                                                            |
|---------------------|-----------------------------------------------------------------|
| `@ResponseBody`     | 객체를 JSON으로 직렬화하여 응답 본문으로 전달한다.               |
| `@ResponseStatus`   | 응답 상태 코드를 설정한다 (예: `@ResponseStatus(HttpStatus.CREATED)`). |
| `@RestController`   | `@Controller + @ResponseBody` 조합으로 REST 전용 컨트롤러 선언이다. |
| `@Controller`       | JSP 등의 View를 반환하는 MVC 컨트롤러 선언이다.                 |
| `@RequestMapping`   | 클래스 또는 메서드 단위로 공통 URI 및 HTTP Method를 설정한다.   |

---


<div style="margin-top:80px;"></div>



## 🧱 2. Java 빌더 패턴(Builder Pattern)

### 🔹 개념 정의

빌더 패턴은 많은 필드를 가진 객체를 생성할 때 가독성 있게 생성할 수 있도록 도와주는 생성 패턴이다.  
객체 생성 시 `new`와 생성자 파라미터 순서로 인한 혼란을 방지하고, 메서드 체이닝 방식으로 필드를 초기화할 수 있다.

### 🔹 장점

| 장점 | 설명 |
|------|------|
| 가독성 향상 | 필드명이 메서드로 명시되어 어떤 값인지 직관적으로 보인다. |
| 파라미터 순서 자유 | 생성자와 달리 필드 설정 순서에 제약이 없다. |
| 선택적 필드 설정 | null 허용 필드 또는 선택 필드를 자유롭게 조절할 수 있다. |
| 불변성 유지 | setter 없이 값을 설정하므로 불변 객체 구현에 유리하다. |

<div style="margin-top:80px;"></div>

### 🔹 왜 `new` 없이 사용 가능한가?



`Member.builder()`는 내부적으로 `new Builder()`를 감싸는 정적(static) 팩토리 메서드이므로 외부에서는 `new` 없이 사용할 수 있다.  
객체 생성을 `builder()` 메서드에 위임하기 때문에 사용자는 단순히 메서드 체이닝으로 객체를 만들 수 있다.

---

## 🔧 3. 직접 구현한 빌더 내부로직

```java
public class Member {
    private String uid;
    private String account;
    private String password;
    private String nickName;

    // 빌더 전용 생성자 (외부에서 직접 new 하지 못하게 제한)
    private Member(Builder builder) {
        this.uid = UUID.randomUUID().toString();
        this.account = builder.account;
        this.password = builder.password;
        this.nickName = builder.nickName;
    }

    // 빌더 클래스 선언
    public static class Builder {
        private String account;
        private String password;
        private String nickName;

        public Builder account(String account) {
            this.account = account;
            return this;
        }

        public Builder password(String password) {
            this.password = password;
            return this;
        }

        public Builder nickName(String nickName) {
            this.nickName = nickName;
            return this;
        }

        public Member build() {
            return new Member(this);
        }
    }

    // 정적 메서드로 builder 진입점 제공
    public static Builder builder() {
        return new Builder();
    }
}
````

---

<div style="margin-top:80px;"></div>

### 🧪 사용 예시

```java
Member member = Member.builder()
                      .account("shin")
                      .password("1234")
                      .nickName("승현짱")
                      .build();
```

이와 같이 빌더 패턴은 복잡한 객체 생성을 명확하고 안전하게 도와주는 매우 유용한 패턴이다.



