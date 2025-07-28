# MyBatis

---

## 1. MyBatis 소개

MyBatis는 `JdbcTemplate`과 같은 기존의 데이터베이스 접근 방식의 불편함을 해소하고, 더 강력하고 편리한 데이터베이스 연동을 지원하는 퍼시스턴스 프레임워크이다.

### 1.1. MyBatis의 필요성

`JdbcTemplate`을 사용할 때 발생하는 주요 불편함은 다음과 같다.

1.  **SQL 관리의 어려움**: SQL 문이 자바 코드 내에 문자열 형태로 존재하여 가독성이 떨어지고 유지보수가 어렵다.
2.  **수동적인 객체 매핑**: `ResultSet`에서 데이터를 추출하여 자바 객체로 변환하는 반복적인 코드를 직접 작성해야 한다.
3.  **동적 SQL 작성의 복잡성**: 조건에 따라 SQL 구문이 변경되는 동적 SQL을 자바 코드로 처리하기가 매우 복잡하다.

MyBatis는 이러한 문제점들을 해결하여 개발자가 비즈니스 로직에 더 집중할 수 있도록 돕는다.

### 1.2. MyBatis의 주요 특징

1.  **SQL과 자바 코드의 분리**: SQL 문을 별도의 XML 파일에 작성하여 코드와 쿼리를 명확하게 분리한다.
2.  **자동 객체 매핑**: `ResultSet`의 조회 결과를 자바 객체로 자동으로 매핑해주는 기능을 제공한다.
3.  **동적 SQL 지원**: XML 기반의 태그를 통해 조건에 따라 SQL을 동적으로 생성하는 기능을 강력하게 지원한다.

---

## 2. MyBatis 설정

스프링 부트 환경에서 MyBatis를 사용하기 위한 설정 과정은 다음과 같다.

1.  **의존성 추가** (`build.gradle`)
    ```groovy
    dependencies {
        implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.3'
    }
    ```

2.  **애플리케이션 설정** (`application.properties` 또는 `application.yml`)
    데이터베이스 연결 정보와 함께 MyBatis 관련 설정을 추가한다.
    ```yaml
    spring:
      datasource:
        url: jdbc:mariadb://localhost:3306/mydb
        username: root
        password: 1234

    # MyBatis 설정
    mybatis:
      # XML 매퍼 파일의 위치를 지정한다.
      mapper-locations: classpath:mapper/**/*.xml
      # 도메인 클래스의 패키지를 지정하여 XML에서 별칭으로 사용할 수 있게 한다.
      type-aliases-package: com.example.domain
      # 데이터베이스의 snake_case 컬럼명을 자바의 camelCase 필드명으로 자동 매핑한다.
      configuration:
        map-underscore-to-camel-case: true
    ```

3.  **Mapper 인터페이스 작성**
    SQL을 호출할 자바 인터페이스를 정의하고 `@Mapper` 어노테이션을 붙인다.
    ```java
    @Mapper
    public interface UserMapper {
        User findById(Long id);
        List<User> findAll();
        void save(User user);
    }
    ```

4.  **XML 매퍼 파일 작성**
    `mapper-locations`에 지정한 경로(`src/main/resources/mapper/`)에 XML 파일을 생성한다. `namespace`는 매퍼 인터페이스의 전체 경로와 일치해야 한다.
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.example.mapper.UserMapper">
        <select id="findById" resultType="User">
            SELECT * FROM users WHERE id = #{id}
        </select>
    </mapper>
    ```

---

## 3. XML 매퍼 작성

XML 매퍼는 SQL 문을 정의하고 관리하는 핵심 파일이다.

### 3.1. 주요 SQL 요소

| 태그 | 설명 | 주요 속성 |
|---|---|---|
| `<select>` | 데이터 조회(SELECT) | `id`, `parameterType`, `resultType`, `resultMap` |
| `<insert>` | 데이터 삽입(INSERT) | `id`, `parameterType`, `useGeneratedKeys`, `keyProperty` |
| `<update>` | 데이터 수정(UPDATE) | `id`, `parameterType` |
| `<delete>` | 데이터 삭제(DELETE) | `id`, `parameterType` |

-   `id`: 매퍼 인터페이스의 메서드 이름과 일치시킨다.
-   `parameterType`: SQL문에 전달될 파라미터의 타입을 지정한다. (선택 사항)
-   `resultType`: 조회 결과를 매핑할 객체의 타입을 지정한다.
-   `useGeneratedKeys`: `true`로 설정 시 자동 생성된 키(예: AUTO_INCREMENT) 값을 반환받을 수 있다.
-   `keyProperty`: `useGeneratedKeys`로 생성된 키 값을 저장할 파라미터 객체의 필드명을 지정한다.

### 3.2. 파라미터 바인딩

1.  **`#{}` (값 바인딩)**
    -   `PreparedStatement`의 `?` 파라미터와 동일하게 동작한다.
    -   전달된 값을 안전하게 바인딩하므로 SQL 인젝션 공격을 방지한다. **항상 사용을 권장한다.**

2.  **`${}` (문자열 치환)**
    -   전달된 값을 문자열 그대로 SQL문에 삽입한다.
    -   테이블명이나 컬럼명처럼 동적으로 변해야 하는 식별자에 사용될 수 있으나, **SQL 인젝션에 매우 취약하므로 사용자 입력값에는 절대 사용하면 안 된다.**

### 3.3. resultMap 사용

데이터베이스 컬럼명과 자바 객체의 필드명이 다를 경우, `<resultMap>`을 사용하여 명시적으로 매핑 규칙을 정의할 수 있다. 이는 복잡한 조인 결과를 객체 그래프로 매핑할 때 유용하다.

```xml
<resultMap id="bookWithCategory" type="Book">
    <id property="id" column="book_id"/>
    <result property="title" column="title"/>
    <!-- Association: 1:1 관계 매핑 -->
    <association property="category" javaType="BookCategory">
        <id property="id" column="category_id"/>
        <result property="name" column="category_name"/>
    </association>
</resultMap>

<select id="selectBookWithCategory" resultMap="bookWithCategory">
    SELECT b.id as book_id, b.title, c.id as category_id, c.name as category_name
    FROM books b
    JOIN book_categories c ON b.category_id = c.id
    WHERE b.id = #{id}
</select>
```

### 3.4. 공통 SQL 활용

`<sql>` 태그로 공통 SQL 조각을 정의하고, `<include>` 태그로 이를 재사용하여 코드 중복을 줄이고 유지보수성을 높일 수 있다.

```xml
<sql id="userColumns">
    id, name, email, age, created_at
</sql>

<select id="findAll" resultType="User">
    SELECT <include refid="userColumns"/>
    FROM users
</select>
```

---

## 4. 동적 SQL

MyBatis의 가장 강력한 기능 중 하나로, 조건에 따라 SQL을 동적으로 구성할 수 있게 한다.

### 4.1. 주요 동적 SQL 요소

| 태그 | 설명 |
|---|---|
| `<if>` | 가장 기본적인 조건문. `test` 속성의 조건이 참일 때 내부 SQL을 포함한다. |
| `<choose>`, `<when>`, `<otherwise>` | 자바의 `switch-case` 문과 유사하게 여러 조건 중 하나만 선택하여 처리한다. |
| `<where>` | `WHERE` 절을 동적으로 생성한다. 조건이 하나 이상일 때만 `WHERE` 키워드를 추가하고, 불필요한 `AND`나 `OR`를 자동으로 제거한다. |
| `<set>` | `UPDATE` 문의 `SET` 절을 동적으로 생성한다. 불필요한 쉼표(`,`)를 자동으로 제거한다. |
| `<foreach>` | 컬렉션(List, Array 등) 데이터를 반복 처리한다. 주로 `IN` 절을 생성할 때 사용된다. |

### 4.2. 동적 SQL 실습 예제: 상품 검색 시스템

사용자의 다양한 검색 조건(상품명, 가격대, 카테고리 등)을 처리하는 동적 SQL 예제이다.

**검색 조건 클래스 (DTO)**
```java
@Getter @Setter
public class ProductSearchCondition {
    private String name;
    private List<Long> categoryIds;
    private Integer minPrice;
    private Integer maxPrice;
    private Boolean inStock;
}
```

**XML 매퍼**
```xml
<mapper namespace="com.shop.mapper.ProductMapper">
    <select id="searchProducts" parameterType="ProductSearchCondition" resultType="Product">
        SELECT *
        FROM products
        <where>
            <if test="name != null and name != ''">
                AND name LIKE CONCAT('%', #{name}, '%')
            </if>
            <if test="minPrice != null">
                AND price >= #{minPrice}
            </if>
            <if test="maxPrice != null">
                AND price &lt;= #{maxPrice}
            </if>
            <if test="inStock != null and inStock">
                AND stock_quantity > 0
            </if>
            <if test="categoryIds != null and !categoryIds.empty">
                AND category_id IN
                <foreach collection="categoryIds" item="categoryId" open="(" separator="," close=")">
                    #{categoryId}
                </foreach>
            </if>
        </where>
        ORDER BY created_at DESC
    </select>
</mapper>
```

---

## 5. 데이터 접근 계층 설계

효율적이고 유지보수하기 좋은 애플리케이션을 구축하기 위해서는 데이터 접근 계층을 체계적으로 설계하는 것이 중요하다.

### 5.1. 계층형 아키텍처

일반적으로 웹 애플리케이션은 다음과 같은 3계층 구조를 가진다.

1.  **프레젠테이션 계층 (Controller)**: 사용자 요청을 받고 응답을 반환한다.
2.  **비즈니스 계층 (Service)**: 핵심 비즈니스 로직과 트랜잭션을 처리한다.
3.  **데이터 접근 계층 (Repository/Mapper)**: 데이터베이스와의 통신을 전담한다.

### 5.2. 데이터 접근 계층의 역할과 구조

-   데이터베이스 종속적인 기술(MyBatis)을 캡슐화하여 비즈니스 계층이 데이터 접근 기술에 대해 알지 못하도록 분리한다.
-   **단일 책임 원칙(SRP)**에 따라 데이터 영속성 처리라는 하나의 책임에만 집중한다.

일반적으로 다음과 같은 구조로 설계한다.

1.  **도메인 객체 (Entity/DTO)**: 데이터의 구조를 정의한다.
2.  **매퍼 인터페이스 (`@Mapper`)**: MyBatis가 구현체를 자동으로 생성하며, XML과 1:1로 매핑된다.
3.  **Repository 인터페이스**: 데이터 접근 메서드를 정의한다. 비즈니스 계층은 이 인터페이스에만 의존한다.
4.  **Repository 구현체 (`@Repository`)**: Repository 인터페이스를 구현하며, 내부적으로 매퍼 인터페이스를 호출하여 실제 데이터베이스 작업을 수행한다.

```java
// Service는 Repository 인터페이스에만 의존
@Service
public class ProductService {
    private final ProductRepository productRepository;
    // ...
}

// Repository 인터페이스
public interface ProductRepository {
    Optional<Product> findById(Long id);
    Product save(Product product);
}

// Repository 구현체 (MyBatis 기술 사용)
@Repository
public class MyBatisProductRepository implements ProductRepository {
    private final ProductMapper productMapper; // MyBatis 매퍼 주입

    @Override
    public Optional<Product> findById(Long id) {
        return Optional.ofNullable(productMapper.selectById(id));
    }
    // ...
}```

---

## 6. 예외 처리와 로깅

안정적인 시스템 운영을 위해 예외 상황을 적절히 처리하고, 시스템의 동작 상태를 기록하는 것은 필수적이다.

### 6.1. 예외 처리 전략

-   **사용자 정의 예외**: 비즈니스 규칙에 위배되는 상황(예: 재고 부족, 중복된 데이터)을 표현하기 위해 명확한 의미를 가진 사용자 정의 예외 클래스를 생성한다.
-   **계층적 예외**: `BusinessException`과 같은 최상위 예외를 정의하고, 구체적인 예외들이 이를 상속받도록 하여 체계적으로 관리한다.
-   **전역 예외 처리**: `@RestControllerAdvice`를 사용하여 컨트롤러 계층에서 발생하는 예외를 한곳에서 처리하고, 일관된 형식의 오류 응답을 클라이언트에게 반환한다.

```java
// 전역 예외 처리기
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusinessException(BusinessException e) {
        log.error("비즈니스 예외 발생: code={}, message={}", e.getErrorCode(), e.getMessage());
        ErrorResponse response = new ErrorResponse(e.getErrorCode(), e.getMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(response);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleException(Exception e) {
        log.error("처리되지 않은 예외 발생", e);
        ErrorResponse response = new ErrorResponse("INTERNAL_SERVER_ERROR", "시스템 오류가 발생했습니다.");
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(response);
    }
}
```

### 6.2. 로깅 전략

-   **로그 레벨 활용**: 상황의 심각도에 따라 `ERROR`, `WARN`, `INFO`, `DEBUG`, `TRACE` 레벨을 적절히 사용한다.
    -   `ERROR`: 시스템 장애, 데이터베이스 연결 실패 등 심각한 오류
    -   `WARN`: 잠재적인 문제(예: 재고 부족 경고)
    -   `INFO`: 주요 처리 흐름(예: 주문 완료, 서버 시작)
    -   `DEBUG`: 개발 및 디버깅에 필요한 상세 정보
-   **민감 정보 마스킹**: 로그에 개인정보나 금융정보(카드 번호 등)가 노출되지 않도록 반드시 마스킹 처리한다.
-   **로그 설정**: `application.yml` 또는 `logback-spring.xml` 파일을 통해 로그 레벨, 출력 형식, 파일 저장 위치 등을 환경별(개발, 운영)로 다르게 설정한다.

# ✅ MyBatis에서 `enum` 과 `생성자` 매핑 정리 (실무용 핵심 요약)

---

## 💡 1. `enum` 자동 매핑은 어떻게 작동하나?

### ✅ 핵심 원리

> MyBatis는 DB에서 가져온 문자열을 `Enum.valueOf()`로 자동 매핑한다.
> 즉, **DB 값과 enum 상수명이 정확히 일치하면** 별도 설정 없이 자동 처리됨.

---

### 📌 예시

```java
// 1. enum 선언
public enum Position {
    P, C, OUT
}

// 2. Entity 클래스
public class Player {
    private Position position;
}

// 3. DB 값
position = 'P'

// 4. 매핑 XML
<result column="position" property="position" />
```

→ 내부적으로 MyBatis는 이렇게 처리함:

```java
player.setPosition(Position.valueOf("P")); // 자동 호출됨
```

---

### 💬 필드(설명값)가 없으면?

```java
public enum Position {
    P, C, OUT
}
```

* `valueOf("P")` → `Position.P` 반환
* `System.out.println(pos)` → `P` 출력
* → 그냥 **상수명 자체가 출력되고, 잘 매핑됨**

---

### 💥 주의사항

| 조건                       | 결과                                   |
| ------------------------ | ------------------------------------ |
| DB 값이 `P`인데 enum에 `P` 없음 | ❌ 예외 발생 (`IllegalArgumentException`) |
| DB에 `투수` 저장됨             | ❌ `valueOf("투수")` → 실패               |
| 대소문자 불일치 (`p`)           | ❌ 실패 (대소문자 구분함)                      |

---

## 🧠 핵심 요약 – enum 파트

| 조건        | 설명                               |
| --------- | -------------------------------- |
| 필드 없어도 됨  | 매핑 기준은 `name()` (상수명)            |
| 자동 매핑 O   | `Enum.valueOf(DB값)` 호출           |
| DB에 저장할 값 | 반드시 enum 상수명 (`"P"`, `"OUT"` 등)  |
| 필드 있는 경우  | `.getDescription()` 같은 출력용으로만 활용 |

---

---

## 🚫 2. MyBatis에서 생성자 주의사항

### ❗ 문제 상황

```java
public Player(ResultSet rs) { ... }
```

이렇게 `ResultSet`을 받는 생성자만 있고, **기본 생성자 없음**
→ `resultType="Player"`로 사용할 때 MyBatis가 객체 생성 실패
→ **필드 매핑이 전부 깨지거나 예외 발생** (`Unknown position` 같은)

---

### ✅ 해결 방법

```java
public class Player {
    public Player() {} // ✅ 반드시 필요!

    public Player(ResultSet rs) { ... } // 이건 있어도 됨
}
```

> 🔥 **MyBatis는 오직 기본 생성자만 사용해서 객체를 만든다!**
> 나머지 생성자는 그냥 무시함 (있어도 됨)

---

## 🧠 핵심 요약 – 생성자 파트

| 상황                   | 가능 여부           | 설명 |
| -------------------- | --------------- | -- |
| 기본 생성자만 있음           | ✅ OK            |    |
| 기본 생성자 + 다른 생성자 있음   | ✅ OK            |    |
| 다른 생성자만 있고 기본 생성자 없음 | ❌ 실패 (객체 생성 불가) |    |

---

## ✅ 최종 슬로건 요약

> 🧠 **MyBatis는 enum은 알아서 `valueOf()`로 매핑하고,
> 객체는 오직 기본 생성자를 통해 만든다!**
