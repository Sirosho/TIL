# 🌿 Spring JDBC 핵심 구성요소 정리


## ✅ 1. 주요 클래스 및 인터페이스

| 분류           | 이름                           | 설명                                                      |
| ------------ | ---------------------------- | ------------------------------------------------------- |
| ⭐ 핵심 클래스     | `JdbcTemplate`               | JDBC 사용을 단순화한 핵심 클래스. 대부분 이걸 씀.                         |
| Named SQL 지원 | `NamedParameterJdbcTemplate` | `:name` 형식의 파라미터 지원. 가독성 ↑                              |
| 간단한 insert   | `SimpleJdbcInsert`           | INSERT를 더 간결하게 처리하게 해줌                                  |
| 간단한 call     | `SimpleJdbcCall`             | 저장 프로시저 호출 시 사용                                         |
| 결과 매핑        | `RowMapper<T>`               | 한 행 → 자바 객체로 매핑                                         |
| 결과 매핑        | `ResultSetExtractor<T>`      | ResultSet 전체 → 객체 하나로 추출                                |
| 파라미터 바인딩     | `PreparedStatementSetter`    | `?` 파라미터 값 바인딩할 때 사용                                    |
| SQL 정의       | `SqlParameterSource`         | 이름 기반 파라미터 전달 시 사용 (`BeanPropertySqlParameterSource` 등) |

---

<div style="margin-top:80px;"></div>

## ✅ 2. JdbcTemplate 주요 메서드

| 메서드                | 설명                              | 리턴값                         |
| ------------------ | ------------------------------- | --------------------------- |
| `update()`         | `INSERT`, `UPDATE`, `DELETE` 실행 | `int` (영향받은 행 수)            |
| `batchUpdate()`    | 여러 건 한 번에 실행                    | `int[]`                     |
| `query()`          | 여러 행 조회 (→ RowMapper 필요)        | `List<T>`                   |
| `queryForObject()` | 단일 행 단일 결과                      | `T`                         |
| `queryForList()`   | 여러 행, 여러 컬럼                     | `List<Map<String, Object>>` |
| `queryForMap()`    | 단일 행, 여러 컬럼                     | `Map<String, Object>`       |
| `execute()`        | 복잡한 SQL이나 DDL                   | void 또는 사용자 정의              |
| `call()`           | 저장 프로시저 호출용 (거의 안 씀)            | `Map<String, Object>`       |

---

<div style="margin-top:80px;"></div>

## ✅ 3. 자주 쓰는 RowMapper 구현체

| 구현체                        | 설명                                |
| -------------------------- | --------------------------------- |
| `BeanPropertyRowMapper<T>` | 컬럼명 = 필드명 → 자동 매핑                 |
| `ColumnMapRowMapper`       | 컬럼명 → 값 형태의 `Map<String, Object>` |
| `SingleColumnRowMapper<T>` | 단일 컬럼 결과를 매핑 (문자열, 숫자 등)          |
| `(rs, rowNum) -> {...}`    | 람다식으로 직접 구현                       |

---

<div style="margin-top:80px;"></div>

## ✅ 4. NamedParameterJdbcTemplate 관련

| 메서드                                                      | 설명             |
| -------------------------------------------------------- | -------------- |
| `update(String sql, Map<String, ?> params)`              | 이름 기반 SQL 파라미터 |
| `query(String sql, Map<String, ?> params, RowMapper<T>)` | query 버전       |
| `queryForObject(...)`, `queryForList(...)` 등도 지원         |                |

> 📌 SQL 예시:

```sql
INSERT INTO users (name, age) VALUES (:name, :age)
```


<div style="margin-top:80px;"></div>

## ✅ 5. 기타 유용한 클래스들

| 클래스                              | 설명                           |
| -------------------------------- | ---------------------------- |
| `BeanPropertySqlParameterSource` | 자바 객체의 프로퍼티 → 이름 기반 파라미터로 매핑 |
| `MapSqlParameterSource`          | 이름 기반 파라미터를 Map 형태로 전달할 때 사용 |
| `GeneratedKeyHolder`             | insert 후 자동 증가된 키(pk) 받기용    |

---

<div style="margin-top:80px;"></div>






# ✅ RowMapper 매핑 구현체 작동 순서 정리


## 💡 전체 흐름 요약

```
1. JdbcTemplate.query(...) 호출
    ↓
2. 내부에서 SQL 실행 → ResultSet 얻음
    ↓
3. ResultSet 반복 (rs.next())
    ↓
4. 각 행마다 rowMapper.mapRow(rs, rowNum) 호출
    ↓
5. mapRow() 내부에서 ResultSet → 객체 변환 (필드 세팅)
    ↓
6. 변환된 객체를 List<T>에 add()
    ↓
7. 모든 행 반복 후 → List<T> 반환
```

<div style="margin-top:80px;"></div>


## 🔍 상세 순서 예시 (BeanPropertyRowMapper 기준)

```java
List<User> users = jdbcTemplate.query(
    "SELECT * FROM users",
    new BeanPropertyRowMapper<>(User.class)
);
```

<div style="margin-top:80px;"></div>

### 👉 내부 동작 순서

| 단계 | 동작                    | 설명                                            |
| -- | --------------------- | --------------------------------------------- |
| ①  | SQL 실행                | JDBC로 PreparedStatement 실행                    |
| ②  | ResultSet 수신          | DB에서 SELECT 결과 테이블 받음                         |
| ③  | 반복 시작                 | `while (rs.next())` 시작                        |
| ④  | rowNum 증가             | 첫 행부터 rowNum 0, 1, 2, ...                     |
| ⑤  | mapRow(rs, rowNum) 호출 | 매 행마다 RowMapper가 호출됨                          |
| ⑥  | 객체 생성                 | BeanPropertyRowMapper는 리플렉션으로 `newInstance()` |
| ⑦  | 컬럼-필드 매핑              | 컬럼명 → 자바 필드명 매핑 (setXxx() 또는 필드 접근)           |
| ⑧  | List에 add()           | 완성된 객체를 리스트에 추가                               |
| ⑨  | 종료 후 반환               | 리스트 반환 (`List<User>`)                         |

---

<div style="margin-top:80px;"></div>

## ✨ BeanPropertyRowMapper 내부 작동 요약

```java
T obj = newInstance(T); // 빈 객체 생성

for (각 컬럼명) {
    String property = convertColumnToProperty(column); // 컬럼명 → 필드명 매핑
    Object value = rs.getObject(column);
    BeanUtils.setProperty(obj, property, value); // setter or field로 값 주입
}

return obj;
```

> 📌 컬럼명과 필드명이 정확히 매칭되어야 정상 동작함
> `user_name` → `userName`처럼 자동 매핑 안 되는 경우도 있음 (이땐 커스텀 RowMapper 필요!)

---

<div style="margin-top:80px;"></div>

## 🛠 커스텀 RowMapper는?

```java
public class UserRowMapper implements RowMapper<User> {
    public User mapRow(ResultSet rs, int rowNum) {
        return new User(
            rs.getLong("id"),
            rs.getString("name"),
            rs.getInt("age")
        );
    }
}
```

> 직접 `rs.getXxx("컬럼명")` 으로 데이터를 꺼내서 객체를 만들어줌.
> BeanPropertyRowMapper보다 **유연하고 안전**함

---

<div style="margin-top:80px;"></div>

##  요약

| 구현체                     | 방식                          | 특징                           |
| ----------------------- | --------------------------- | ---------------------------- |
| `BeanPropertyRowMapper` | 리플렉션 + 필드명 자동 매핑            | 빠르지만 컬럼-필드 불일치 시 문제          |
| `RowMapper<T>` 직접 구현    | rs.getXxx()로 수동 매핑          | 가장 명확하고 안정적                  |
| `ColumnMapRowMapper`    | Map\<String, Object> 형태로 매핑 | 빠르게 키-값 조회할 때                |
| `SingleColumnRowMapper` | 단일 컬럼만 매핑                   | 이름 목록, 숫자 리스트 등 단순 SELECT 용도 |


## 예시코드


```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Optional;

@Repository
public class PlayerRepository {

    private final JdbcTemplate jdbcTemplate;

    public PlayerRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    // RowMapper 정의
    private final RowMapper<Player> playerRowMapper = (rs, rowNum) -> {
        Player player = new Player();
        player.setId(rs.getLong("id"));
        player.setName(rs.getString("name"));
        player.setAge(rs.getInt("age"));
        player.setPosition(rs.getString("position"));
        player.setWar(rs.getDouble("war"));
        return player;
    };

    // 1. INSERT
    public void save(Player player) {
        String sql = "INSERT INTO players (name, age, position, war) VALUES (?, ?, ?, ?)";
        jdbcTemplate.update(sql,
                player.getName(),
                player.getAge(),
                player.getPosition(),
                player.getWar());
    }

    // 2. SELECT ALL
    public List<Player> findAll() {
        String sql = "SELECT * FROM players";
        return jdbcTemplate.query(sql, playerRowMapper);
    }

    // 3. SELECT BY ID
    public Optional<Player> findById(Long id) {
        String sql = "SELECT * FROM players WHERE id = ?";
        List<Player> result = jdbcTemplate.query(sql, playerRowMapper, id);
        return result.stream().findFirst();
    }

    // 4. UPDATE
    public int update(Long id, String position, double war) {
        String sql = "UPDATE players SET position = ?, war = ? WHERE id = ?";
        return jdbcTemplate.update(sql, position, war, id);
    }

    // 5. DELETE
    public int delete(Long id) {
        String sql = "DELETE FROM players WHERE id = ?";
        return jdbcTemplate.update(sql, id);
    }
}
```