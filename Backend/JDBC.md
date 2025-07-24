# JDBC 작업 순서 및 객체별 메서드 정리

## ✅ 은행 비유로 보는 JDBC 작업 순서

| 단계  | 역할       | 객체                                  | 비유                |
| --- | -------- | ----------------------------------- | ----------------- |
| 1단계 | DB 연결 열기 | `Connection`                        | 창구 번호표 받고 창구 점유   |
| 2단계 | SQL 준비하기 | `PreparedStatement`                 | 창구 직원에게 업무 요청서 작성 |
| 3단계 | SQL 실행하기 | `executeQuery()`, `executeUpdate()` | 요청서 제출            |
| 4단계 | 결과 처리    | `ResultSet`                         | 업무 처리 결과 받기       |
| 5단계 | 자원 정리    | `close()`                           | 창구 퇴장 및 정리        |

---

## ✅ 객체별 주요 메서드 정리

### 📌 `Connection` (DB 연결 객체)

| 메서드                      | 설명                      |
| ------------------------ | ----------------------- |
| `prepareStatement(sql)`  | SQL 실행 준비를 위한 객체 생성     |
| `setAutoCommit(boolean)` | 트랜잭션 자동 커밋 설정 (기본 true) |
| `commit()`               | 트랜잭션 커밋                 |
| `rollback()`             | 트랜잭션 롤백                 |
| `close()`                | DB 연결 종료 (반드시 해야 함)     |

---

### 📌 `PreparedStatement` (SQL 실행 준비 객체)

| 메서드                    | 설명                                         |
| ---------------------- | ------------------------------------------ |
| `setXXX(index, value)` | SQL문의 `?`에 값 설정 (예: setString, setInt 등)   |
| `executeQuery()`       | `SELECT` 실행, 결과는 `ResultSet` 반환            |
| `executeUpdate()`      | `INSERT`, `UPDATE`, `DELETE` 실행, 변경 행 수 반환 |
| `execute()`            | 위 두 개를 통합한 일반 실행용                          |
| `close()`              | SQL 실행 객체 종료                               |

---

### 📌 `ResultSet` (쿼리 결과 집합 객체)

| 메서드             | 설명                                |
| --------------- | --------------------------------- |
| `next()`        | 다음 행으로 이동, 없으면 false 반환           |
| `getXXX("컬럼명")` | 열 값 가져오기 (예: getString, getInt 등) |
| `wasNull()`     | 마지막 읽은 값이 NULL인지 확인               |
| `close()`       | 결과 셋 종료                           |

---

## 🔁 전체 작업 흐름 요약 예시

```java
Connection conn = null;
PreparedStatement pstmt = null;
ResultSet rs = null;

try {
    conn = DriverManager.getConnection(url, user, pw); // 1단계

    String sql = "SELECT * FROM users WHERE name = ?";
    pstmt = conn.prepareStatement(sql);               // 2단계
    pstmt.setString(1, "홍길동");

    rs = pstmt.executeQuery();                         // 3단계

    while (rs.next()) {                                // 4단계
        String email = rs.getString("email");
        System.out.println("이메일: " + email);
    }
} catch (SQLException e) {
    e.printStackTrace();
} finally {
    try { if (rs != null) rs.close(); } catch (Exception e) {}
    try { if (pstmt != null) pstmt.close(); } catch (Exception e) {}
    try { if (conn != null) conn.close(); } catch (Exception e) {} // 5단계
}
```

---

##  요약

```plaintext
Connection         ← DB 연결 (창구 잡기)
  ↳ prepareStatement → SQL 준비
       ↳ setXXX       → 값 바인딩
       ↳ executeXXX   → 실행
           ↳ ResultSet → 결과 처리
```


## JDBC의 예외처리


* JDBC는 모든 오류 상황을 SQLException (체크 예외)으로 던짐.
* 그래서 JDBC 코드는 반드시 try-catch 또는 throws로 예외 처리를 해야 함.