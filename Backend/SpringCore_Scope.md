# 2.5 스프링의 스코프(Bean Scopes)

스프링에서 빈(Bean)은 IoC 컨테이너가 생성 및 관리하는 객체이며, 스코프(Scope)를 통해 빈의 생명주기와 사용 범위를 지정할 수 있다. 스코프 설정에 따라 빈이 생성되고 파괴되는 시점이 달라진다.

---

## 1. 주요 빈 스코프 종류

| 스코프 이름 | 사용 환경 | 생명주기 기준 |
|-------------|-----------|----------------|
| `singleton` | 일반 / 웹 | 애플리케이션 시작 ~ 종료 |
| `prototype` | 일반 / 웹 | 매번 요청 시마다 |
| `request`   | 웹 전용   | HTTP 요청 단위 |
| `session`   | 웹 전용   | 사용자 세션 단위 |
| `application` | 웹 전용 | 서블릿 컨텍스트 단위 |

---

## 1.1  싱글톤 스코프 (`singleton`) default 

- **설명**: 스프링의 기본 스코프. IoC 컨테이너 당 하나의 인스턴스만 생성됨.
- **사용 예시**: 대부분의 서비스 빈, 설정 빈 등
- **코드 예시**:
```java
@Service
@Scope("singleton")
public class SingletonService { }
````

---

## 1.2 프로토타입 스코프 (`prototype`)

* **설명**: 요청할 때마다 새로운 인스턴스를 생성.
* **사용 예시**: 매번 다른 상태가 필요한 객체, 동적 설정 객체
* **코드 예시**:

```java
@Service
@Scope("prototype")
public class PrototypeService { }
```

---

## 1.3 요청 스코프 (`request`)

* **설명**: HTTP 요청마다 새로운 빈이 생성됨. 요청 종료 시 소멸됨.
* **사용 예시**: 요청에 특화된 데이터 처리 빈
* **코드 예시**:

```java
@Component
@Scope("request")
public class RequestScopedBean { }
```

---

## 1.4 세션 스코프 (`session`)

* **설명**: HTTP 세션 당 하나의 인스턴스가 생성됨. 세션 종료 시 소멸됨.
* **사용 예시**: 로그인 사용자 정보, 세션 상태 저장
* **코드 예시**:

```java
@Component
@Scope("session")
public class SessionScopedBean { }
```

---

## 1.5 애플리케이션 스코프 (`application`)

* **설명**: 서블릿 컨텍스트 기준으로 생성되며 애플리케이션 생명주기와 동일하게 유지됨.
* **사용 예시**: 전역 설정 정보, 캐시 객체
* **코드 예시**:

```java
@Component
@Scope("application")
public class ApplicationScopedBean { }
```

---

## 2. 요약 표

| 스코프           | 설명                 | 웹 전용 여부 |
| ------------- | ------------------ | ------- |
| `singleton`   | IoC 컨테이너 당 1개 객체생성 | ❌       |
| `prototype`   | 요청마다 새 객체생성        | ❌       |
| `request`     | HTTP 요청마다 새 객체생성   | ✅       |
| `session`     | HTTP 세션마다 새 객체생성   | ✅       |
| `application` | 서블릿 컨텍스트 당 1개 객체생성 | ✅       |

---

## 결론

* `singleton`은 가장 일반적이며 기본 설정이다.
* `prototype`은 상태가 분리된 객체를 매번 생성해야 할 때 유용하다.
* `request`, `session`, `application`은 웹 애플리케이션 전용 스코프이며, 각 요청 또는 세션 별로 다른 객체를 관리할 수 있다.
* 올바른 스코프 설정은 애플리케이션의 메모리 관리 및 안정성 향상에 큰 기여를 한다.

* 자주 만들어줘야 할 것 같은 객체는 어노테이션을 안거는게 낫다.
