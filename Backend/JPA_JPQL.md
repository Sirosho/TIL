# 📌 JPQL (Java Persistence Query Language) 정리

## 1. **JPQL 기본 문법**

* **SQL과 비슷하지만, 테이블이 아니라 엔터티(Entity)와 필드명을 기준으로 작성**
* SELECT 절에는 **엔터티나 엔터티의 필드명**을 사용
* FROM 절에는 **테이블명이 아니라 엔터티명**을 사용 (보통 클래스명)
<div style="margin-top:80px;"></div>

### ✅ 기본 예시

```java
@Query("SELECT s FROM Student s WHERE s.city = ?1")
List<Student> findByCity(String city);
```

➡️ 변환되는 SQL

```sql
SELECT * FROM tbl_student WHERE city = ?
```

---

<div style="margin-top:80px;"></div>

## 2. **JPQL vs SQL 차이**

| 구분    | JPQL        | SQL        |
| ----- | ----------- | ---------- |
| 대상    | **엔터티 클래스** | 데이터베이스 테이블 |
| 필드    | **엔티티 필드명** | 컬럼명        |
| 반환    | 엔터티/DTO/필드  | ResultSet  |
| 실행 시점 | 애플리케이션 레벨   | DB 레벨      |

---

<div style="margin-top:80px;"></div>

## 3. **파라미터 바인딩 방법**

### 위치 기반 (`?1`, `?2`)

```java
@Query("SELECT s FROM Student s WHERE s.city = ?1 AND s.major = ?2")
List<Student> findByCityAndMajor(String city, String major);
```

### 이름 기반 (`:파라미터명`)

```java
@Query("SELECT s FROM Student s WHERE s.city = :city")
List<Student> findByCity(@Param("city") String city);
```

⚡ 이름 기반 바인딩이 가독성과 유지보수에 더 유리해서 실무에서 권장됨.

---

<div style="margin-top:80px;"></div>

## 4. **DTO로 조회**

엔터티 전체 말고 특정 필드만 조회하고 싶을 때:

```java
@Query("SELECT new com.example.dto.StudentDTO(s.name, s.city) FROM Student s WHERE s.city = :city")
List<StudentDTO> findStudentDTOByCity(@Param("city") String city);
```

➡️ `new` 키워드를 사용 → DTO 생성자 호출

---

<div style="margin-top:80px;"></div>

## 5. **JPQL 사용 시 주의할 점**

1. **엔터티명과 필드명 사용**

   * 테이블명/컬럼명이 아니라 **엔터티 클래스명 + 필드명**
   * 잘못 쓰면 런타임 에러 발생

2. **`*` 사용 불가**

   ```sql
   SELECT s FROM Student s  -- ✅ 올바름
   SELECT * FROM Student s  -- ❌ JPQL에서 에러
   ```

3. **반환 타입 일치**

   * `SELECT s` → 반환 타입은 `Student`
   * `SELECT s.name` → 반환 타입은 `String` (또는 DTO)

4. **엔티티 관계는 Join으로 탐색**

   ```java
   @Query("SELECT s FROM Student s JOIN s.courses c WHERE c.name = :courseName")
   List<Student> findByCourse(@Param("courseName") String courseName);
   ```

5. **Native SQL과 혼동 금지**

   * `@Query(nativeQuery = true, value = "SELECT * FROM tbl_student")` → 이건 **SQL**
   * JPQL은 `nativeQuery=false` (기본값)

6. **동적 쿼리는 어려움**

   * 조건이 상황마다 바뀌는 경우 → QueryDSL이나 Specification을 사용 권장

---

<div style="margin-top:80px;"></div>

## 6. **정리된 예시**

```java
public interface StudentRepository extends JpaRepository<Student, String> {

    // JPQL 기본
    @Query("SELECT s FROM Student s WHERE s.city = :city")
    List<Student> findByCity(@Param("city") String city);

    // 특정 필드만 반환
    @Query("SELECT s.name FROM Student s WHERE s.age >= :age")
    List<String> findNamesByAge(@Param("age") int age);

    // DTO로 반환
    @Query("SELECT new com.example.dto.StudentDTO(s.name, s.city) FROM Student s")
    List<StudentDTO> findStudentDTOs();
}
```

---

## ✅ 요약

* JPQL = **엔터티 중심 쿼리** (SQL과 다름)
* **테이블/컬럼명 X → 엔터티/필드명 O**
* 위치 기반(`?1`)보다 이름 기반(`:param`) 권장
* DTO 매핑 시 `new` 사용
* 동적 쿼리에는 한계가 있으므로 QueryDSL 병행 권장




<div style="margin-top:80px;"></div>


# DTO로 조회 추가설명

## 1. DTO로 조회하는 이유

1. **필요한 데이터만 가져오기**

   * 엔티티 조회: 모든 컬럼 SELECT
   * DTO 조회: 지정한 컬럼만 SELECT
     → 불필요한 DB I/O 감소

2. **성능 최적화**

   * 조회 필드 수가 적을수록 DB → 애플리케이션 전송량 감소
   * JPA 매핑해야 할 데이터 감소 → CPU 및 메모리 사용량 절약

3. **조회 전용 객체 제공**

   * DTO는 불변 객체로 설계 가능 (Setter 제거)
   * 화면 출력, API 응답에 필요한 형태로 가공 가능

4. **엔티티 보호**

   * 엔티티 직접 노출 방지 → 캡슐화 강화, 유지보수 용이성 확보

---

<div style="margin-top:80px;"></div>

## 2. 성능 차이

### 엔티티 조회

```java
List<Student> students = studentRepository.findAll();
```

실행 SQL

```sql
SELECT id, name, age, city, major, created_at 
FROM tbl_student;
```

* 모든 컬럼 조회 → 네트워크 전송량 증가
* 모든 필드 매핑 필요 → JPA 매핑 비용 증가
* 불필요한 데이터까지 메모리에 적재

---

### DTO 조회

```java
@Query("SELECT new com.example.dto.StudentDTO(s.name, s.city) FROM Student s")
List<StudentDTO> findStudentDTOs();
```

실행 SQL

```sql
SELECT name, city 
FROM tbl_student;
```

* 필요한 컬럼만 조회 → 네트워크 전송량 감소
* DTO 생성자 호출 (필요 필드만 매핑)
* 대량 조회 시 효율적

---

<div style="margin-top:80px;"></div>

## 3. 성능 비교 표

| 항목           | 엔티티 조회       | DTO 조회               |
| ------------ | ------------ | -------------------- |
| SELECT 컬럼 수  | 모든 컬럼        | 필요한 컬럼만              |
| DB → App 전송량 | 많음           | 적음                   |
| 매핑 비용        | 엔티티 전체 매핑    | DTO 생성자 (필드 일부)      |
| 메모리 사용량      | 큼            | 작음                   |
| 추천 상황        | 수정/저장 필요한 경우 | 조회 전용 API, 대량 데이터 조회 |

---

<div style="margin-top:80px;"></div>

## 4. Projection (인터페이스 기반) 비교

Projection은 Spring Data JPA가 인터페이스를 기반으로 필요한 필드만 매핑해주는 기능이다.
DTO 클래스를 작성하지 않아도 된다는 장점이 있으나, 복잡한 로직이나 가공에는 한계가 있다.

### 예시

```java
public interface StudentProjection {
    String getName();
    String getCity();
}

@Query("SELECT s.name AS name, s.city AS city FROM Student s")
List<StudentProjection> findStudentProjections();
```

---

<div style="margin-top:80px;"></div>

## 5. JPA 조회 방식 종합 비교

| 구분                | 엔티티 조회                      | DTO 조회 (JPQL `new`)      | Projection 조회          |
| ----------------- | --------------------------- | ------------------------ | ---------------------- |
| SQL SELECT        | 모든 컬럼                       | 필요한 컬럼만                  | 필요한 컬럼만                |
| JPA 매핑 비용         | 엔티티 전체 매핑                   | DTO 생성자 호출               | 프록시 객체 생성              |
| DB I/O / 네트워크 전송량 | 많음                          | 적음                       | 적음                     |
| 타입 안정성            | 높음                          | 높음                       | 높음                     |
| 코드 가독성            | 높음                          | 보통                       | 높음                     |
| 장점                | 모든 필드 사용 가능, 연관관계 탐색 자유     | 불필요한 컬럼 제외, 메모리 효율 ↑     | DTO 불필요, 가볍고 유지보수 쉬움   |
| 단점                | 불필요한 데이터까지 조회, 대규모 조회 시 비효율 | DTO 클래스 작성 필요, 생성자 맞춰야 함 | 복잡한 로직 한계, 중첩 구조 지원 제한 |
| 추천 사용처            | 단건 조회, 수정/저장 필요한 경우         | 조회 전용 API, 대량 데이터 조회     | 조회 전용 화면, 간단한 컬럼 매핑    |

---

<div style="margin-top:80px;"></div>

## ✅ 결론

* 엔티티 조회는 **CRUD 및 수정/저장 로직**에 적합하다.
* DTO 조회는 **조회 전용 API**에서 성능과 효율성을 높이는 데 유리하다.
* Projection 조회는 **간단한 화면 출력용**으로 가볍게 사용하기 좋다.

**실무 권장 패턴**

* CRUD 작업 → 엔티티 조회
* 조회 전용 API, 대규모 리스트/페이징 → DTO 조회
* 단순 조회 화면 → Projection 조회









<div style="margin-top:100px;"></div>





# 📌 @Query 어노테이션 구조

Spring Data JPA의 `@Query`는 다음과 같은 속성을 가질 수 있다:

```java
@Query(
    value = "JPQL or Native SQL",
    nativeQuery = false,
    countQuery = "JPQL for count"
)
```

<div style="margin-top:80px;"></div>

## ✅ 주요 속성

| 속성              | 설명                                      | 기본값                   |
| --------------- | --------------------------------------- | --------------------- |
| **value**       | 실행할 JPQL 또는 SQL                         | 필수 (생략 시 첫 번째 값으로 인식) |
| **nativeQuery** | `true`면 Native SQL 사용, `false`면 JPQL 사용 | false                 |
| **countQuery**  | 페이징 처리 시 전체 개수를 세는 별도 쿼리 지정             | 없음                    |

---

<div style="margin-top:80px;"></div>

## ✅ 왜 `value =` 를 생략할 수 있나?

자바 어노테이션 규칙 때문이야.

* 어노테이션의 \*\*속성이 하나만 있고 이름이 `value`\*\*라면
  속성명을 생략할 수 있음.

### 예시 (동일 의미)

```java
@Query("SELECT s FROM Student s WHERE s.city = ?1")
List<Student> findByCity(String city);

@Query(value = "SELECT s FROM Student s WHERE s.city = ?1")
List<Student> findByCity(String city);
```

둘 다 똑같이 동작 ✅

---

<div style="margin-top:80px;"></div>

## ✅ 괄호 안에 들어올 수 있는 다른 경우

### 1. `nativeQuery` 같이 사용

```java
@Query(
    value = "SELECT * FROM tbl_student WHERE city = ?1",
    nativeQuery = true
)
List<Student> findByCityNative(String city);
```

### 2. 페이징에서 `countQuery` 추가

```java
@Query(
    value = "SELECT s FROM Student s WHERE s.city = :city",
    countQuery = "SELECT COUNT(s) FROM Student s WHERE s.city = :city"
)
Page<Student> findByCity(@Param("city") String city, Pageable pageable);
```

---

<div style="margin-top:80px;"></div>

## 📌 정리

* `@Query`에서 `value=`는 생략 가능 (자바 어노테이션 기본 규칙)
* 하지만 `nativeQuery`, `countQuery` 등 **추가 속성과 같이 쓸 때**는 구분을 위해 `value=`를 명시하는 경우가 많음
* 따라서 **간단한 JPQL 한 줄**이면 생략해도 되지만,
  **속성이 여러 개일 땐 value= 를 붙여주는 게 가독성과 안정성에 좋음**

