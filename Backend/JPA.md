# Spring Data JPA


## 1. Spring Data JPA란?

Spring Data JPA는 Spring Framework의 하위 프로젝트 중 하나이다. 이것은 자바 애플리케이션에서 관계형 데이터베이스의 데이터를 손쉽게 다루기 위한 표준 기술인 JPA(Java Persistence API)를 더욱 편리하게 사용하도록 지원한다. 복잡한 JPA 구현을 단순화하여 개발자가 비즈니스 로직에 더 집중할 수 있게 하는 역할을 한다.

### 1.1. JPA (Java Persistence API)

JPA는 Java EE5부터 도입된 API로, 자바 개발자가 관계형 데이터베이스(RDBMS)를 객체지향적으로 다룰 수 있도록 고안되었다. 이는 ORM(Object-Relational Mapping) 표준 기술로서, 자바 클래스와 데이터베이스 테이블 간의 매핑 정보를 이용해 SQL 쿼리 없이 데이터를 조작할 수 있게 한다.

### 1.2. Spring Data JPA의 등장과 기능

Spring Data JPA는 2011년에 처음 출시되었으며, 기본적인 CRUD(Create, Read, Update, Delete) 작업에 대해 별도의 구현 코드 없이 Repository 인터페이스를 통해 간단하게 사용할 수 있도록 제공한다.

**주요 기능**
- **Repository 인터페이스**: 기본적인 CRUD 연산 메서드를 자동으로 제공한다.
- **쿼리 메서드**: 메서드 이름만으로 간단한 쿼리를 생성하고 실행한다.
- **페이징과 정렬**: 데이터 조회 시 페이징과 정렬을 간편하게 처리한다.
- **동적 쿼리**: 조건에 따라 동적으로 쿼리를 생성할 수 있다.

---

## 2. JPA 기본 설정

### 2.1. application.properties와 application.yml 비교

Spring Boot 프로젝트의 설정 파일로, 기능은 동일하지만 문법과 형식이 다르다.

#### 1. application.properties
- 전통적인 Java Properties 파일 형식(`key=value`)을 사용한다.
- 구조가 단순하여 빠르고 간단한 편집에 유리하다.
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=root
```

#### 2. application.yml
- YAML(YAML Ain't Markup Language) 형식(`key: value`)을 사용한다.
- 들여쓰기를 통해 계층 구조를 표현하므로, 복잡한 구성 설정에서 가독성이 높다.
```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: root
```

### 2.2. 프로젝트에 기본 설정 적용하기

1. 프로젝트 IDE(IntelliJ, Eclipse 등)를 연다.
2. `src/main/resources` 디렉토리의 `application.properties` 파일명을 `application.yml`로 변경한다.
3. 아래와 같이 데이터베이스 연결 및 JPA 설정을 작성한다.

```yaml
server:
  port: 8181

spring:
  datasource:
    url: jdbc:mariadb://localhost:3306/jpa
    username: root
    password: 1234
    driver-class-name: org.mariadb.jdbc.Driver
  jpa:
    database-platform: org.hibernate.dialect.MariaDB53Dialect
    hibernate:
      ddl-auto: create
    show-sql: true
```

### 2.3. Spring Boot 설정: hibernate.ddl-auto

엔티티 클래스를 기반으로 데이터베이스 스키마를 자동으로 관리하는 옵션이다.

- **none**: 아무 작업도 수행하지 않는다.
- **validate**: 엔티티 모델과 데이터베이스 스키마가 일치하는지 검증한다.
- **update**: 기존 스키마를 유지하면서 변경된 부분만 엔티티 모델에 맞춰 업데이트한다. 개발 환경에서 유용하다.
- **create**: 애플리케이션 실행 시마다 기존 스키마를 삭제하고 새로 생성한다. 초기 개발 및 테스트 환경에 적합하다.
- **create-drop**: `create`와 유사하지만, 애플리케이션 종료 시 스키마를 삭제한다.

> **주의**: `update`, `create`, `create-drop` 옵션은 데이터 손실의 위험이 있으므로 운영 환경(Production)에서는 사용하지 않고, 스키마 변경을 수동으로 관리하는 것이 일반적이다.

---

## 3. 엔티티(Entity)와 매핑

### 3.1. JPA에서 사용되는 주요 어노테이션

| 어노테이션 | 설명 |
| :--- | :--- |
| **`@Entity`** | 클래스를 JPA가 관리하는 엔티티로 지정한다. |
| **`@Table`** | 엔티티와 매핑될 데이터베이스 테이블을 지정한다. (생략 시 클래스 이름으로 매핑) |
| **`@Id`** | 필드를 해당 엔티티의 기본 키(Primary Key)로 지정한다. |
| **`@GeneratedValue`** | 기본 키의 생성 전략(AUTO, IDENTITY, SEQUENCE, TABLE)을 지정한다. |
| **`@Column`** | 필드와 매핑될 테이블의 컬럼을 상세히 지정한다. (이름, null 여부, 길이 등) |
| **`@Transient`** | 해당 필드를 데이터베이스에 매핑하지 않도록 지정한다. |
| **`@Temporal`** | `java.util.Date`, `java.util.Calendar` 타입을 날짜(DATE), 시간(TIME), 또는 둘 다(TIMESTAMP)로 매핑한다. |
| **`@Enumerated`** | Java의 `enum` 타입을 매핑한다. 순서(ORDINAL) 또는 이름(STRING)으로 저장할 수 있다. |
| **`@Lob`** | BLOB, CLOB 등 대용량 데이터를 저장할 때 사용한다. |
| **`@CreationTimestamp`** | 엔티티 생성 시 시간을 자동으로 기록한다. |
| **`@UpdateTimestamp`** | 엔티티 수정 시 시간을 자동으로 기록한다. |

### 3.2. 엔티티 생성 예시

```java
package com.study.jpa.entity;

import lombok.*;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import javax.persistence.*;
import java.time.LocalDateTime;

@Getter @Setter
@ToString @EqualsAndHashCode
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
@Table(name = "tbl_product")
public class Product {

    @Id
    @Column(name = "product_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "product_name", nullable = false)
    private String name;

    @Builder.Default
    @Enumerated(EnumType.STRING)
    private Category category = Category.FOOD;

    @Column(nullable = false)
    private Integer price;

    @CreationTimestamp
    @Column(updatable = false, nullable = false)
    private LocalDateTime createdDate;

    @UpdateTimestamp
    @Column(name = "updated_date")
    private LocalDateTime updatedDate;

    public enum Category {
        FOOD,
        FASHION,
        ELECTRONIC
    }
}
```

### 3.3. 기본 키(Primary Key) 생성 전략

`@GeneratedValue` 어노테이션의 `strategy` 속성으로 지정한다.

1.  **GenerationType.AUTO (기본값)**: JPA 구현체가 데이터베이스에 따라 적절한 전략을 자동으로 선택한다.
2.  **GenerationType.IDENTITY**: 데이터베이스가 기본 키 생성을 위임한다. (예: MySQL의 `AUTO_INCREMENT`) 키 생성을 위해 `INSERT` 쿼리 실행 후 ID를 가져온다.
3.  **GenerationType.SEQUENCE**: 데이터베이스의 시퀀스 객체를 사용하여 기본 키를 할당받는다. (예: Oracle, PostgreSQL)
4.  **GenerationType.TABLE**: 키 생성 전용 테이블을 사용하여 모든 테이블의 기본 키를 관리한다. 데이터베이스 종류에 상관없이 사용 가능하나 성능상 불리할 수 있다.
5.  **UUID**: `@GeneratedValue`를 사용하지 않고 `@PrePersist`와 같은 콜백 메서드를 통해 직접 UUID를 생성하여 할당하는 방식도 있다. 분산 환경에서 고유성을 보장하기에 적합하다.

---

## 4. Repository 인터페이스

### 4.1. Repository 인터페이스 생성

Spring Data JPA에서는 `JpaRepository` 인터페이스를 상속받는 것만으로 간단하게 Repository를 생성할 수 있다.

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface ProductRepository extends JpaRepository<Product, Long> {
    // Product: 관리할 엔티티 클래스
    // Long: 해당 엔티티의 기본 키(ID) 타입
}
```

### 4.2. 기본 제공 CRUD 메서드

`JpaRepository`를 상속하면 아래와 같은 주요 메서드들이 자동으로 제공된다.

- `save(S entity)`: 엔티티를 저장하거나 수정한다.
- `findById(ID id)`: ID로 엔티티를 조회한다. `Optional`을 반환한다.
- `findAll()`: 모든 엔티티를 조회한다.
- `count()`: 엔티티의 총 개수를 반환한다.
- `delete(T entity)`: 주어진 엔티티를 삭제한다.
- `deleteById(ID id)`: ID로 엔티티를 삭제한다.
- `existsById(ID id)`: ID에 해당하는 엔티티의 존재 여부를 반환한다.

### 4.3. 테스트 예시

`@SpringBootTest` 어노테이션과 JUnit5를 사용하여 Repository의 동작을 테스트한다.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import static org.junit.jupiter.api.Assertions.*;
import java.util.Optional;

@SpringBootTest
class ProductRepositoryTest {

    @Autowired
    ProductRepository productRepository;

    @Test
    void testSave() {
        Product product = Product.builder()
                .name("Test Product")
                .price(10000)
                .build();
        Product savedProduct = productRepository.save(product);
        assertNotNull(savedProduct.getId());
    }

    @Test
    void testFindById() {
        Product product = Product.builder()
                .name("Find Product")
                .price(20000)
                .build();
        Product savedProduct = productRepository.save(product);

        Optional<Product> foundProduct = productRepository.findById(savedProduct.getId());

        assertTrue(foundProduct.isPresent());
        assertEquals("Find Product", foundProduct.get().getName());
    }

    // ... 기타 CRUD 테스트
}
```

---

## 5. 쿼리 메서드 (Query Methods)
Spring Data JPA에서 메서드 이름을 규칙에 맞게 작성하면, SQL(JPQL) 쿼리를 자동으로 생성해주는 기능이다.
JpaRepository를 상속받은 인터페이스에 익명매서드로 선언하여 사용한다.


### 5.1. 쿼리 메서드 작성 규칙

- `find...By`, `read...By`, `get...By`, `query...By`, `search...By`, `stream...By`: 조회를 수행한다. `find`가 가장 일반적으로 사용된다.
- `count...By`: 조회 결과의 개수를 반환한다.
- `exists...By`: 조회 결과의 존재 여부를 `boolean`으로 반환한다.
- `delete...By` 또는 `remove...By`: 조회된 엔티티를 삭제한다.

메서드 이름의 `...` 부분에는 엔티티의 필드 이름과 조건 키워드를 조합하여 작성한다.

**주요 조건 키워드**
- `And`, `Or`: 여러 조건을 조합한다. (예: `findByNameAndPrice`)
- `LessThan`, `GreaterThan`, `Between`: 크기를 비교한다. (예: `findByPriceLessThan`)
- `IsNull`, `IsNotNull`: `null` 여부를 확인한다.
- `Like`, `NotLike`, `StartingWith`, `EndingWith`, `Containing`: 문자열 패턴을 검색한다.
- `OrderBy`: 결과를 정렬한다. (예: `findByNameOrderByIdDesc`)

### 5.2. 쿼리 메서드 사용 예시

`Product` 엔티티를 기준으로 다음과 같이 쿼리 메서드를 정의할 수 있다.

```java
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface ProductRepository extends JpaRepository<Product, Long> {

    // 이름으로 상품 목록 조회
    List<Product> findByName(String name);

    // 특정 가격보다 비싼 상품 목록 조회
    List<Product> findByPriceGreaterThan(Integer price);

    // 특정 카테고리에 속하고, 가격을 내림차순으로 정렬하여 조회
    List<Product> findByCategoryOrderByPriceDesc(Product.Category category);

    // 이름에 특정 문자열이 포함된 상품이 존재하는지 확인
    boolean existsByNameContaining(String keyword);
}
```

### 5.3. 주의사항
쿼리 메서드는 메서드 이름 자체가 쿼리문이므로 오타나 잘못된 필드 이름을 사용하면 애플리케이션 시작 시점에 오류가 발생한다. 또한, 이 기능은 비교적 간단한 쿼리에 매우 유용하지만, 복잡하고 동적인 쿼리가 필요한 경우에는 `@Query` 어노테이션을 사용해 직접 JPQL을 작성하거나 QueryDSL과 같은 외부 라이브러리를 사용하는 것이 더 효과적이다.