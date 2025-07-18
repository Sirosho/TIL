# 스프링 빈(Bean) 관리

스프링에서 **빈(Bean)**은 스프링 IoC 컨테이너가 생성 및 관리하는 객체를 의미한다. 빈은 애플리케이션의 구성 요소로서 생성, 설정, 주입, 소멸까지의 생명주기를 가진다.

---

<div style="margin-top:80px;"></div>

## 1. 스프링 빈의 정의와 생명주기

### 1.1 스프링 빈의 정의

- 스프링 빈은 스프링 IoC 컨테이너에 의해 생성 및 관리되는 객체이다.
- 빈은 XML 설정, 자바 설정 클래스, 또는 애너테이션을 통해 정의된다.

<div style="margin-top:80px;"></div>

### 1.2 스프링 빈의 생명주기

스프링 빈의 생명주기는 다음과 같다:

1. **빈 정의 (Bean Definition)**  
   XML, 자바 설정 클래스, 애너테이션을 통해 빈의 정의가 이루어진다.

2. **빈 생성 (Bean Creation)**  
   컨테이너가 빈 정의를 바탕으로 객체를 생성한다.
3. **의존성 주입 (Dependency Injection)**  
   생성된 객체에 필요한 의존성이 주입된다.
4. **초기화 (Initialization)**  
   초기화 메서드 또는 `@PostConstruct`로 초기 작업을 수행한다.
5. **사용 (Usage)**  
   애플리케이션에서 빈이 사용된다.
6. **소멸 (Destruction)**  
   컨테이너 종료 시점에 소멸 메서드 또는 `@PreDestroy`가 호출된다.

---

<div style="margin-top:80px;"></div>

## 2. 빈 생성과 관리 방법

스프링은 다양한 방식으로 빈을 생성하고 관리할 수 있다.

<div style="margin-top:50px;"></div>

### 2.1 XML 설정

- 스프링 초창기 방식이며, `applicationContext.xml` 파일 등에 빈 정의가 포함된다.

```xml
<bean id="greetingService" class="com.example.GreetingServiceImpl">
    <property name="message" value="Hello, World!" />
</bean>
````

---

### 2.2 자바 설정 (Java Configuration)

* `@Configuration` 클래스에서 `@Bean` 메서드를 통해 빈을 등록한다.

```java
@Configuration
public class AppConfig {
    @Bean
    public GreetingService greetingService() {
        GreetingServiceImpl service = new GreetingServiceImpl();
        service.setMessage("Hello, World!");
        return service;
    }
}
```

---

### 2.3 애너테이션 기반 설정

* 클래스에 애너테이션을 붙여 빈으로 등록하며, 컴포넌트 스캔 기능을 통해 자동 탐지된다.

```java
@Service
public class GreetingServiceImpl implements GreetingService {
    private String message;

    @Override
    public String greet() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

---


<div style="margin-top:50px;"></div>

## 3. 주요 애너테이션 역할 구분

| 애너테이션         | 역할                             |
| ------------- | ------------------------------ |
| `@Component`  | 일반적인 스프링 빈 정의                  |
| `@Service`    | 서비스(비즈니스 로직) 계층 빈 정의           |
| `@Repository` | DAO(데이터 접근 계층) 빈 정의 및 예외 변환 처리 |
| `@Controller` | 웹 요청을 처리하는 컨트롤러 정의             |

---

<div style="margin-top:50px;"></div>

## 요약

* **스프링 빈**은 IoC 컨테이너가 생성 및 관리하는 객체이다.
* 빈의 **생명주기**는 정의 → 생성 → 주입 → 초기화 → 사용 → 소멸의 단계를 따른다.
* 빈 등록 방식은 다음과 같다:

  * XML 설정 방식
  * 자바 설정 방식 (`@Configuration`, `@Bean`)
  * 애너테이션 기반 방식 (`@Component`, `@Service`, 등)
* 역할에 따라 구분된 애너테이션을 활용하면 계층 간 책임이 명확해진다.

> ✅ 스프링 빈은 객체 생명주기와 의존성을 통합적으로 관리하여 **유지보수성, 유연성, 테스트 용이성**을 높인다.


<div style="margin-top:80px;"></div>

# 애너테이션 기반 구성

스프링 프레임워크는 애너테이션을 통해 빈 등록과 의존성 주입을 간결하게 처리할 수 있는 기능을 제공한다.

---

## 1. `@Autowired`를 사용한 의존성 주입

`@Autowired`는 스프링 IoC 컨테이너가 자동으로 적절한 빈을 찾아서 주입해주는 애너테이션이다. 다음 세 가지 방식으로 사용 가능하다:

### 1.1 생성자 주입

```java
@Service
public class MyService {

    private final MyRepository myRepository;

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }
}
````

* 가장 권장되는 방식이며, 불변성과 테스트 용이성을 보장한다.

### 1.2 세터 주입

```java
@Service
public class MyService {

    private MyRepository myRepository;

    @Autowired
    public void setMyRepository(MyRepository myRepository) {
        this.myRepository = myRepository;
    }
}
```

* 선택적 의존성 또는 주입 시점의 유연함이 필요할 때 사용한다.

### 1.3 필드 주입

```java
@Service
public class MyService {

    @Autowired
    private MyRepository myRepository;
}
```

* 코드가 간결하지만 테스트와 유지보수 측면에서 비추천된다.

---

<div style="margin-top:80px;"></div>


## 2. `@Qualifier`를 사용한 특정 빈 주입

같은 타입의 빈이 여러 개 존재할 경우, `@Qualifier`를 사용하여 특정 빈을 명시적으로 지정할 수 있다.

```java
@Autowired
public MyService(@Qualifier("specificRepository") MyRepository myRepository) {
    this.myRepository = myRepository;
}
```

* `specificRepository`라는 이름을 가진 빈이 주입된다.
* `camelCase` 로 적어주어야 한다

* `@Qualifier`는 `@Component("이름")`, `@Service("이름")` 등으로 이름 지정 가능하다.

---

<div style="margin-top:80px;"></div>

## 3. `@ComponentScan`을 사용한 컴포넌트 스캔

`@ComponentScan`은 지정된 패키지를 스캔하여, 그 안에 존재하는 컴포넌트 클래스들을 자동으로 빈으로 등록하는 설정이다.

```java
@Configuration
@ComponentScan(basePackages = "com.example.corestudy.chap02_04")
public class AppConfig {
    // 해당 패키지 내 컴포넌트(@Component, @Service 등)를 자동 등록
}
```

* 스캔 대상: `@Component`, `@Service`, `@Repository`, `@Controller` 등
* 하위 패키지까지 포함하여 자동으로 탐지된다.

---

<div style="margin-top:80px;"></div>

## 요약

| 애너테이션            | 설명                    |
| ---------------- | --------------------- |
| `@Autowired`     | 타입 기반 의존성 자동 주입       |
| `@Qualifier`     | 동일 타입 빈 중 특정 이름의 빈 주입 |
| `@ComponentScan` | 지정된 패키지 내 컴포넌트 자동 등록  |

> ✅ 애너테이션 기반 설정은 XML 설정보다 간결하고 직관적이며, 유지보수성과 생산성을 높이는 데 효과적이다.


<div style="margin-top:80px;"></div>

## 추가 팁

* @Configuration 과
@ComponentScan 은 Spring boot에서 자동으로 걸어준다 SpringBootApplication 어노테이션에 포함이 되어있다.


### @Qualifier 사용법

``` java
@Qualifier 

인터페이스 사용 등으로 생성자에 의존객체가 여러개여야 할 경우
어떤 의존객체를 Bean으로 불러올지 지정하는 어노테이션, 생성자 파라미터 앞에 붙인다

  public StudentController(@Qualifier("studentRepository") StudentRepository repository) {
        this.repository = repository;
    }

이렇게 camelCase로 적어줌

@Repository("sdr") 이렇게 퀄리파이어에서 쓰일 이름을 지정해줄수 있음

이렇게 하면
public StudentController(@Qualifier("smr") StudentRepository repository)
이렇게 불러올수있음
```