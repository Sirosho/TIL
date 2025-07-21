# Java의 Exception 분류

## 1. ✅ Checked Exception (컴파일 예외)
* 컴파일 시점에 반드시 처리해야 하는 예외 (try-catch 또는 throws 필수)

* 외부 자원(IO, DB, 파일 등)과 관련된 예외가 많음

| 예외 클래스                   | 설명                       |
| ------------------------ | ------------------------ |
| `IOException`            | 파일, 네트워크 등 입출력 에러        |
| `SQLException`           | 데이터베이스 관련 에러             |
| `ParseException`         | 날짜, 숫자 등의 파싱 에러          |
| `ClassNotFoundException` | 클래스를 찾지 못함 (리플렉션 등에서 발생) |


```
"체크 예외는 예외 처리를 강제하기 때문에, try-catch 또는 throws로 처리하지 않으면 아예 실행조차 못한다."
```











## 2. ✅ Unchecked Exception (런타임 예외)

* 컴파일러가 강제하지 않음
* 개발자가 논리적으로 체크하고 막아야 하는 예외
* RuntimeException을 상속한 모든 예외가 해당됨

| 예외 클래스                           | 설명                 |
| -------------------------------- | ------------------ |
| `NullPointerException`           | null 값을 잘못 참조      |
| `ArrayIndexOutOfBoundsException` | 배열 인덱스 범위 초과       |
| `IllegalArgumentException`       | 잘못된 인자 전달          |
| `IllegalStateException`          | 잘못된 상태에서 호출        |
| `ArithmeticException`            | 수학적 오류 (예: 0으로 나눔) |
| `NumberFormatException`          | 문자열을 숫자로 파싱 실패     |

## 3.✅ Spring에서 자주 다루는 예외들

| 예외 클래스                                    | 설명                      |
| ----------------------------------------- | ----------------------- |
| `HttpClientErrorException`                | REST 요청 시 4xx 오류        |
| `HttpServerErrorException`                | REST 요청 시 5xx 오류        |
| `MethodArgumentNotValidException`         | `@Valid` 검증 실패          |
| `MissingServletRequestParameterException` | 요청 파라미터 누락              |
| `NoHandlerFoundException`                 | 잘못된 URL로 접근 시           |
| `DataIntegrityViolationException`         | DB 제약조건 위반 (ex. UNIQUE) |
| `EmptyResultDataAccessException`          | DB 조회 결과 없음             |
| `AccessDeniedException`                   | 보안 관련 권한 거부             |
| `BindException`                           | form 데이터 바인딩 실패         |
