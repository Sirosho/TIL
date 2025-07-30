
# 📌 JPA 제약조건 및 어노테이션 정리

## 1. `@Column` 속성

```java
@Column(
    name = "prod_nm",        // DB 컬럼명
    nullable = false,        // NOT NULL
    unique = true,           // UNIQUE
    length = 30,             // VARCHAR 길이
    precision = 10,          // 전체 자리수 (숫자용)
    scale = 2,               // 소수점 자리수 (숫자용)
    columnDefinition = "TEXT", // 직접 SQL 정의
    insertable = true,       // INSERT 포함 여부
    updatable = true         // UPDATE 포함 여부
)
```

<div style="margin-top:80px;"></div>

| 속성                 | 설명                     | 예시                          |
| ------------------ | ---------------------- | --------------------------- |
| `name`             | 컬럼명 지정 (기본값: 필드명)      | `name = "prod_nm"`          |
| `nullable`         | `NOT NULL` 제약          | `nullable = false`          |
| `unique`           | `UNIQUE` 제약            | `unique = true`             |
| `length`           | 문자열 길이 (VARCHAR)       | `length = 50`               |
| `precision`        | 전체 자리수 (`BigDecimal`용) | `precision = 10`            |
| `scale`            | 소수 자리수 (`BigDecimal`용) | `scale = 2`                 |
| `columnDefinition` | DB 컬럼 정의 직접 입력         | `columnDefinition = "TEXT"` |
| `insertable`       | INSERT SQL 포함 여부       | `insertable = false`        |
| `updatable`        | UPDATE SQL 포함 여부       | `updatable = false`         |

---

<div style="margin-top:80px;"></div>

## 2. 키 / ID 관련

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```


| 어노테이션             | 설명                                                |
| ----------------- | ------------------------------------------------- |
| `@Id`             | PK 지정                                             |
| `@GeneratedValue` | 키 자동 생성 (`IDENTITY`, `SEQUENCE`, `AUTO`, `TABLE`) |

---

<div style="margin-top:80px;"></div>

## 3. 유니크 제약

```java
@Entity
@Table(
    name = "product",
    uniqueConstraints = {
        @UniqueConstraint(columnNames = {"category_id", "prod_nm"})
    }
)
public class Product { ... }
```

| 어노테이션                         | 설명          |
| ----------------------------- | ----------- |
| `@Table(uniqueConstraints=…)` | 복합 유니크 키 제약 |

---

<div style="margin-top:80px;"></div>

## 4. 문자열 제약 (Hibernate Validator)

```java
@NotNull
@NotBlank
@Size(min = 2, max = 30)
```

| 어노테이션             | 설명             |
| ----------------- | -------------- |
| `@NotNull`        | null 불가        |
| `@NotBlank`       | 공백 제외 not null |
| `@Size(min, max)` | 문자열 길이 제한      |

---

<div style="margin-top:80px;"></div>

## 5. 숫자 제약

```java
@Min(1)
@Max(100)
@Positive
@Digits(integer = 5, fraction = 2)
```

| 어노테이션                   | 설명          |
| ----------------------- | ----------- |
| `@Min / @Max`           | 값의 최소/최대 범위 |
| `@Positive / @Negative` | 양수/음수 제약    |
| `@Digits`               | 정수/소수 자리 제한 |

---

<div style="margin-top:80px;"></div>

## 6. 날짜 제약

```java
@Temporal(TemporalType.DATE)
@PastOrPresent
@Future
```

| 어노테이션              | 설명                     |
| ------------------ | ---------------------- |
| `@Temporal`        | DATE/TIME/TIMESTAMP 지정 |
| `@Past`, `@Future` | 과거/미래 값 제약             |
| `@PastOrPresent`   | 오늘 또는 과거               |

---

<div style="margin-top:80px;"></div>

## 7. 외래키 / 연관관계 제약

```java
@ManyToOne
@JoinColumn(name="category_id", nullable=false)
private Category category;
```

| 어노테이션                                                  | 설명                                           |
| ------------------------------------------------------ | -------------------------------------------- |
| `@JoinColumn`                                          | FK 지정 (`nullable`, `unique`, `updatable` 가능) |
| `@ManyToOne`, `@OneToMany`, `@OneToOne`, `@ManyToMany` | 연관관계 매핑                                      |

---

<div style="margin-top:80px;"></div>

# ✅ 정리 요약

* **컬럼 제약**: `nullable`, `unique`, `length`, `precision`, `scale`, `columnDefinition`
* **키 제약**: `@Id`, `@GeneratedValue`
* **유니크 제약**: `@Table(uniqueConstraints=…)`
* **값 검증**: `@NotNull`, `@Size`, `@Min`, `@Max`, `@Past` 등
* **관계 제약**: `@JoinColumn(nullable=false)`





<div style="margin-top:80px;"></div>




# 📌 `spring.jpa.hibernate.ddl-auto` 옵션 종류별 정리

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: update
```

---

<div style="margin-top:80px;"></div>

## 1. none (기본적으로 권장)

* **기능**: 아무 동작도 하지 않음 (스키마 자동 생성/수정 ❌).
* **특징**:

  * DB 스키마는 개발자가 직접 SQL로 관리.
  * 실무에서 가장 많이 쓰이는 방식.
* **사용 상황**:

  * 운영 환경 (DB 스키마 변경을 직접 관리할 때)

---

<div style="margin-top:80px;"></div>

## 2. validate

* **기능**: 엔티티 매핑과 DB 테이블 구조가 일치하는지 검증만 함.
* **특징**:

  * ❌ 테이블 생성/수정 없음
  * ✅ 매핑 불일치 시 예외 발생
* **사용 상황**:

  * 운영 환경에서 DB 스키마와 매핑 검증용
* **예시**:
  엔티티에 `@Column(name="username")`이 있는데 DB에 컬럼이 없으면 예외 발생.

---

<div style="margin-top:80px;"></div>

## 3. update

* **기능**: 엔티티 변경 사항을 테이블에 반영 (추가/변경).
* **특징**:

  * ✅ 새 컬럼 추가
  * ✅ 컬럼 크기 변경 (가능한 경우)
  * ❌ 기존 데이터 삭제 없음
  * ❌ 컬럼 삭제는 반영 안 함
* **사용 상황**:

  * 개발/테스트 단계 (편리)
  * 운영 환경에서는 위험 (데이터 불일치 가능)
* **예시**:
  새로운 필드를 엔티티에 추가하면 DB에도 컬럼 자동 추가됨.

---

<div style="margin-top:80px;"></div>

## 4. create

* **기능**: 애플리케이션 시작 시 **테이블 삭제 후 새로 생성**
* **특징**:

  * ❌ 기존 데이터 모두 삭제
  * ✅ 엔티티 기준으로 항상 새로운 스키마 생성
* **사용 상황**:

  * 로컬 학습용 (DB 초기화 필요할 때)
* **예시**:
  매번 실행할 때 DB 초기화 후 깨끗한 상태에서 시작.

---

<div style="margin-top:80px;"></div>

## 5. create-drop

* **기능**: `create`와 동일하지만,
  애플리케이션 종료 시에도 테이블 DROP
* **특징**:

  * 종료 후 DB 흔적까지 싹 지움
* **사용 상황**:

  * 단위 테스트용
* **예시**:
  테스트 케이스 실행 후 DB 정리 자동.

---

<div style="margin-top:80px;"></div>

## 6. 기타 (legacy 옵션)

Hibernate 자체적으로 지원하던 옵션이지만, Spring Boot에서는 잘 안 씀:

* `update-only` (일부 버전에서 지원) → 변경만 허용
* `create-only` → create만 하고 drop은 안 함

---

<div style="margin-top:80px;"></div>

# 📌 요약 비교표

| 옵션          | 테이블 생성 | 수정 | 삭제           | 데이터 유지 | 주 사용처      |
| ----------- | ------ | -- | ------------ | ------ | ---------- |
| none        | ❌      | ❌  | ❌            | ✅      | 운영 환경      |
| validate    | ❌      | ❌  | ❌            | ✅      | 운영 환경 (검증) |
| update      | ⭕      | ⭕  | ❌            | ✅      | 개발 단계      |
| create      | ⭕      | ⭕  | ⭕(drop 후 생성) | ❌      | 학습/개발      |
| create-drop | ⭕      | ⭕  | ⭕(종료 시 drop) | ❌      | 테스트        |

---

<div style="margin-top:80px;"></div>

## ✅ 추천 가이드

* **운영 환경**: `none` 또는 `validate`
* **개발 환경**: `update`
* **테스트 환경**: `create-drop`
