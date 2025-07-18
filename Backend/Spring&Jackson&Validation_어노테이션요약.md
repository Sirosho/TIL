# 📦 Spring 주요 어노테이션 정리

| 어노테이션                     | 설명                 | 위치                | 예시                                                      |
| ------------------------- | ------------------ | ----------------- | ------------------------------------------------------- |
| `@Component`              | 일반 빈 등록            | 클래스               | `@Component public class MyBean {}`                     |
| `@Service`                | 서비스 계층 빈           | 클래스               | `@Service public class MemberService {}`                |
| `@Repository`             | DAO 계층 + 예외변환      | 클래스               | `@Repository public class UserRepository {}`            |
| `@Controller`             | 웹 요청 처리            | 클래스               | `@Controller public class HomeController {}`            |
| `@RestController`         | REST API 응답 (JSON) | 클래스               | `@RestController public class ApiController {}`         |
| `@RequestMapping("/url")` | 공통 URL 매핑          | 클래스 or 메서드        | `@RequestMapping("/api")`                               |
| `@GetMapping("/url")`     | GET 요청 매핑          | 메서드               | `@GetMapping("/hello")`                                 |
| `@PostMapping("/url")`    | POST 요청 매핑         | 메서드               | `@PostMapping("/submit")`                               |
| `@RequestParam("name")`   | 쿼리 파라미터 바인딩        | 메서드 파라미터          | `public String hello(@RequestParam String name)`        |
| `@PathVariable("id")`     | URL 경로 변수 바인딩      | 메서드 파라미터          | `@GetMapping("/user/{id}")`                             |
| `@RequestBody`            | JSON → 객체 매핑       | 메서드 파라미터          | `public ResponseEntity<?> join(@RequestBody User user)` |
| `@ResponseBody`           | 객체 → JSON 반환       | 메서드 or 클래스        | `@ResponseBody public User find()`                      |
| `@Autowired`              | 의존성 자동 주입          | 필드, 생성자, 메서드      | `@Autowired private UserRepo repo;`                     |
| `@Qualifier("name")`      | 특정 빈 주입            | `@Autowired` 파라미터 | `@Autowired @Qualifier("repo1") UserRepo repo;`         |
| `@Configuration`          | 설정 클래스             | 클래스               | `@Configuration public class AppConfig {}`              |
| `@Bean`                   | 수동 등록 빈            | 메서드               | `@Bean public DataSource ds() {}`                       |
| `@ComponentScan`          | 패키지 스캔 지정          | 클래스               | `@ComponentScan("com.example")`                         |
| `@Scope("...")`           | 빈의 생명주기 설정         | 클래스               | `@Scope("prototype")`                                   |
| `@PostConstruct`          | 초기화 실행 메서드         | 메서드               | `@PostConstruct public void init()`                     |
| `@PreDestroy`             | 종료 직전 실행           | 메서드               | `@PreDestroy public void close()`                       |

---

<div style="margin-top:80px;"></div>

# 🧩 Jackson 어노테이션 (JSON 직렬화/역직렬화)

| 어노테이션                        | 설명               | 위치        | 예시                                                           |
| ---------------------------- | ---------------- | --------- | ------------------------------------------------------------ |
| `@JsonProperty("name")`      | JSON key 이름 지정   | 필드        | `@JsonProperty("account") private String email;`             |
| `@JsonIgnore`                | 직렬화/역직렬화 제외      | 필드        | `@JsonIgnore private String password;`                       |
| `@JsonInclude(...)`          | null 등 제외 조건 설정  | 클래스 or 필드 | `@JsonInclude(JsonInclude.Include.NON_NULL)`                 |
| `@JsonFormat(pattern="...")` | 날짜 포맷 지정         | 날짜 필드     | `@JsonFormat(pattern="yyyy-MM-dd") private LocalDate birth;` |
| `@JsonCreator`               | 생성자로 역직렬화 처리     | 생성자       | `@JsonCreator public User(...) {}`                           |
| `@JsonValue`                 | 객체를 특정 값으로 변환    | 메서드       | `@JsonValue public String toJson() {}`                       |
| `@JsonAnyGetter`             | Map → JSON 동적 키  | 메서드       | `@JsonAnyGetter public Map<String, String> getProps()`       |
| `@JsonAnySetter`             | JSON 키 → Map에 저장 | 메서드       | `@JsonAnySetter public void add(String k, String v)`         |
| `@JsonUnwrapped`             | 중첩 객체 펼치기        | 필드        | `@JsonUnwrapped private Address address;`                    |

---

<div style="margin-top:80px;"></div>

# ✅ Validation 어노테이션 (입력값 검증)

| 어노테이션                     | 설명                        | 위치          | 예시                                              |
| ------------------------- | ------------------------- | ----------- | ----------------------------------------------- |
| `@NotNull`                | null 금지                   | 필드          | `@NotNull private String name;`                 |
| `@NotEmpty`               | null + "" 금지 (빈 문자열 허용 X) | 필드          | `@NotEmpty private List<String> tags;`          |
| `@NotBlank`               | null + "  " 금지 (공백도 금지)   | 필드          | `@NotBlank private String content;`             |
| `@Size(min=, max=)`       | 길이/크기 제한                  | 필드          | `@Size(min=2, max=10) private String nickname;` |
| `@Min(숫자)`                | 최소값                       | 숫자 필드       | `@Min(18) private int age;`                     |
| `@Max(숫자)`                | 최대값                       | 숫자 필드       | `@Max(100) private int age;`                    |
| `@Positive` / `@Negative` | 양수/음수 제한                  | 숫자 필드       | `@Positive private int price;`                  |
| `@Email`                  | 이메일 형식 검사                 | 필드          | `@Email private String email;`                  |
| `@Pattern(regexp="...")`  | 정규표현식 검사                  | 필드          | `@Pattern(regexp="^[a-z]+$")`                   |
| `@Past` / `@Future`       | 날짜가 과거/미래여야 함             | `LocalDate` | `@Past private LocalDate birthday;`             |
| `@Valid`                  | 중첩 객체도 검증                 | 필드          | `@Valid private Address address;`               |
| `@Validated`              | 클래스 or 컨트롤러 단위로 전체 유효성 검사 | 클래스         | `@Validated @RestController`                    |

---

📌 **Spring MVC에서 입력값 검증하려면**

```java
@PostMapping("/join")
public ResponseEntity<?> join(@Valid @RequestBody SignUpRequest dto) {
    ...
}
```
- 이렇게 입력된 파라미터 앞에 @Valid를 붙여줘야함
