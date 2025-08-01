# 📌 쿼리DSL

## 1. 쿼리DSL이란?

> \*\*쿼리DSL(QueryDSL)\*\*은 자바 코드로 JPQL/SQL 쿼리를 타입 안전하게 작성할 수 있게 해주는 라이브러리.
> 문자열 기반 JPQL의 단점을 보완해 **컴파일 시점에 오류를 잡고, IDE 자동완성을 지원**하는 것이 가장 큰 장점이다.

---

<div style="margin-top:80px;"></div>

## 2. Q타입(Q-Class)

* QueryDSL이 엔티티(@Entity)를 기반으로 **자동 생성**하는 메타데이터 클래스
* 엔티티의 각 필드를 타입 안전하게 접근할 수 있도록 지원
* 예시:

  ```java
  QStudent student = QStudent.student;
  queryFactory.selectFrom(student)
              .where(student.age.gt(20))
              .fetch();
  ```

👉 `student.age` 는 문자열이 아니라 `NumberPath<Integer>` 객체 →
**컴파일 시점 문법 검사 & IDE 자동완성 가능**

---

<div style="margin-top:80px;"></div>

## 3. JPAQueryFactory

* **QueryDSL 쿼리를 실행하는 핵심 객체**
* 생성 시 반드시 `EntityManager`를 주입해야 함

  ```java
  JPAQueryFactory queryFactory = new JPAQueryFactory(em);
  ```

### 왜 EntityManager 주입이 필수일까?

1. **쿼리 실행 담당자**

   * QueryDSL은 JPQL을 \*\*"작성"\*\*만 하고,
     실제로 **DB에 실행하는 주체는 EntityManager**
   * 즉, QueryDSL = 쿼리 빌더, EntityManager = 쿼리 실행기

2. **영속성 컨텍스트 연동**

   * 엔티티 캐싱(1차 캐시)
   * dirty checking (변경 감지)
   * flush / clear
     → 이런 JPA 핵심 기능을 사용하려면 EntityManager가 필요

3. **트랜잭션 관리**

   * Spring의 `@Transactional`은 내부적으로 EntityManager와 연계
   * QueryDSL이 EntityManager를 통해 JPQL을 실행해야
     같은 트랜잭션 범위 안에서 안전하게 동작

4. **JPA 표준 준수**

   * QueryDSL은 JPA 위에 얹혀 있는 라이브러리라,
     JPA의 실행 엔진인 EntityManager 없이는 JPQL 수행 불가

---

📌 **정리된 설명 추가**

> `JPAQueryFactory`는 JPQL을 타입 안전하게 작성할 수 있게 해주는 QueryDSL의 실행 진입점이다.
> 하지만 QueryDSL 자체는 쿼리를 "만드는" 역할만 하고,
> **실제로 쿼리를 DB에 날리는 주체는 EntityManager**이므로
> 반드시 `EntityManager`를 주입받아야 한다.
> 이렇게 해야 영속성 컨텍스트, 트랜잭션, flush/clear 같은 JPA 기능과 연동된다.


<div style="margin-top:80px;"></div>

## 4. 장점

| 장점           | 설명                                      |
| ------------ | --------------------------------------- |
| **타입 안전성**   | 문자열 오타 방지, 컴파일 시점 에러 검출                 |
| **IDE 자동완성** | 엔티티 필드 기반 자동완성                          |
| **리팩토링 안전**  | 엔티티 필드명 변경 시, Q타입 재생성으로 에러 즉시 확인        |
| **동적 쿼리 편리** | BooleanBuilder, if문 조합 등으로 유연한 조건 작성 가능 |

---

## 5. 사용 예시

### JPQL (문자열 기반) 

```java
List<Student> result = em.createQuery(
    "select s from Student s where s.age > 20", Student.class
).getResultList();
```

⚠️ 위험성

문자열이라 오타나 컬럼명 변경을 컴파일 시점에 못 잡음 → 런타임 에러 발생

조건 추가 시 문자열 더하기(+) 방식 → 가독성 떨어지고 유지보수 어려움

---


<div style="margin-top:80px;"></div>

### QueryDSL (타입 안전)

```java
QStudent student = QStudent.student;

List<Student> result = queryFactory
    .selectFrom(student)
    .where(student.age.gt(20))
    .fetch();
```


✅ 차이점 & 장점

컴파일 시점에 오류 검출 가능 (student.age 타입이 맞지 않으면 에러)

IDE 자동완성 지원 → 오타 위험 감소

동적 쿼리 작성이 깔끔 (if문으로 조건 조립 가능)

---


<div style="margin-top:80px;"></div>

## 6. 언제 쓰면 좋을까?


| 쿼리 난이도                   | 추천 기술           | 예시                            |
| ------------------------ | --------------- | ----------------------------- |
| **간단한 쿼리**               | Spring Data JPA | INSERT, UPDATE, DELETE, 단순 조회 |
| **조금 복잡한 쿼리**            | QueryDSL        | 간단한 JOIN, GROUP BY            |
| **매우 복잡하거나 DBMS 종속적 쿼리** | JDBC / MyBatis  | 윈도우 함수, DB 전용 함수 등            |


---

## 7. 결론

쿼리DSL은

> **"문자열 기반 JPQL의 불편함을 해결하는 타입 안전 쿼리 빌더"**

Spring JPA와 JDBC 사이에서 **복잡한 조회를 깔끔하게 처리**할 때 강력한 무기가 된다.



<div style="margin-top:80px;"></div>

# 📌 JPAQueryFactory 는 자동 빈등록이 안된다.

## 1. Config 클래스에서 Bean 등록

```java
@Configuration
public class QuerydslConfig {

    @PersistenceContext  // 스프링이 EntityManager 주입
    private EntityManager em;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(em);
    }
}
```

---

## 2. Repository에서 간단히 사용

```java
@Repository
@RequiredArgsConstructor
public class StudentRepositoryCustomImpl implements StudentRepositoryCustom {

    private final JPAQueryFactory queryFactory; // Bean 주입됨

    @Override
    public List<Student> findByAgeGreaterThan(int age) {
        QStudent student = QStudent.student;
        return queryFactory
                .selectFrom(student)
                .where(student.age.gt(age))
                .fetch();
    }
}
```

---

## 3. Bean 등록 안 하면?

```java
public void someMethod() {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em); // 매번 직접 생성
    ...
}
```

⚠️ 단점

* 매번 `new JPAQueryFactory(em)` 해야 함
* 코드 중복 증가 + 유지보수 어려움

---

## ✅ 정리

* **기본적으로 JPAQueryFactory는 자동 Bean 등록 ❌**
* Config에서 Bean으로 등록해두면,
  → `@Autowired` / `@RequiredArgsConstructor` 로 어디서든 주입받아 재사용 가능
* 이렇게 하면 **매번 new로 생성하는 번거로움 해결**

---

<div style="margin-top:80px;"></div>



# 📌 QueryDSL 매서드

| SQL 구문                            | QueryDSL 메서드/사용법                                       | 예제 코드                                                                                |
| --------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| **SELECT**                        | `.select()`, `.selectFrom()`                           | `query.select(member).from(member).fetch();`                                         |
| **DISTINCT**                      | `.distinct()`                                          | `query.select(member.username).distinct().from(member).fetch();`                     |
| **JOIN (INNER JOIN)**             | `.join()`                                              | `.selectFrom(member).join(member.team, team).fetch();`                               |
| **LEFT JOIN**                     | `.leftJoin()`                                          | `.selectFrom(member).leftJoin(member.team, team).fetch();`                           |
| **RIGHT JOIN**                    | `.rightJoin()`                                         | `.selectFrom(member).rightJoin(member.team, team).fetch();`                          |
| **FETCH JOIN (즉시 로딩)**            | `.fetchJoin()`                                         | `.selectFrom(member).join(member.team, team).fetchJoin().fetch();`                   |
| **ON 조건**                         | `.on()`                                                | `.join(member.team, team).on(team.name.eq("A팀"));`                                   |
| **CROSS JOIN**                    | `.from().join()`                                       | `.from(member, team).fetch();`                                                       |
| **WHERE**                         | `.where()`                                             | `.where(member.age.gt(20));`                                                         |
| **AND**                           | `.and()` 또는 쉼표`,`                                      | `.where(member.age.gt(20), member.username.eq("승현"));`                               |
| **OR**                            | `.or()`                                                | `.where(member.age.gt(20).or(member.username.eq("승현")));`                            |
| **IN / NOT IN**                   | `.in()`, `.notIn()`                                    | `.where(member.age.in(20, 25, 30));`                                                 |
| **BETWEEN**                       | `.between()`                                           | `.where(member.age.between(20, 30));`                                                |
| **LIKE / NOT LIKE**               | `.like()`, `.notLike()`, `.contains()`                 | `.where(member.username.contains("승"));`                                             |
| **IS NULL / NOT NULL**            | `.isNull()`, `.isNotNull()`                            | `.where(member.team.isNotNull());`                                                   |
| **비교 연산자**                        | `.eq()`, `.ne()`, `.gt()`, `.goe()`, `.lt()`, `.loe()` | `.where(member.age.goe(20).and(member.age.loe(30)));`                                |
| **ORDER BY**                      | `.orderBy()`                                           | `.orderBy(member.age.desc(), member.username.asc());`                                |
| **GROUP BY**                      | `.groupBy()`                                           | `.groupBy(team.name);`                                                               |
| **HAVING**                        | `.having()`                                            | `.groupBy(team.name).having(member.age.avg().gt(25));`                               |
| **LIMIT / OFFSET**                | `.limit()`, `.offset()`                                | `.offset(20).limit(10).fetch();`                                                     |
| **COUNT / AVG / SUM / MAX / MIN** | `.count()`, `.avg()`, `.sum()`, `.max()`, `.min()`     | `.select(member.age.avg());`                                                         |
| **CASE WHEN THEN**                | `.when().then().otherwise()`                           | `member.age.when(20).then("스무살").otherwise("기타")`                                    |
| **EXISTS / NOT EXISTS**           | `JPAExpressions.exists()`, `.notExists()`              | `.where(JPAExpressions.selectOne().from(subMember).where(...).exists());`            |
| **SUBQUERY**                      | `JPAExpressions`                                       | `.where(member.age.eq(JPAExpressions.select(subMember.age.max()).from(subMember)));` |
| **UPDATE**                        | `queryFactory.update()`                                | `.update(member).set(member.username, "변경").where(member.age.lt(20)).execute();`     |
| **DELETE**                        | `queryFactory.delete()`                                | `.delete(member).where(member.age.gt(60)).execute();`                                |
| **INSERT**                        | ❌ (지원 X, JPA persist 사용)                               | `em.persist(new Member("승현"));`                                                      |
| **COALESCE**                      | `Expressions.coalesce()`                               | `.select(Expressions.coalesce(member.nickname, "익명"));`                              |
| **NULLIF**                        | `Expressions.nullif()`                                 | `.select(Expressions.nullif(member.username, "익명"));`                                |


<div style="margin-top:80px;"></div>

📌 **특이사항**

* `INSERT` 는 QueryDSL이 지원하지 않음 → `EntityManager.persist()` 사용.
* `UNION`, `INTERSECT` 같은 집합 연산도 지원하지 않음 → Native SQL 필요.
* `ANY / SOME` 는 컬렉션 연관관계 (`OneToMany`) 에서만 사용 가능.
* `CASE WHEN` 은 단순 값 매핑(`.when().then().otherwise()`) 방식으로만 가능.

