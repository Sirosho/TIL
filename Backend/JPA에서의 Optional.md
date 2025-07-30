# 📌 Optional 정리
<div style="margin-top:80px;"></div>


## 1. Optional 기본 개념

* `Optional<T>`: 값이 있을 수도, 없을 수도 있는 객체를 감싸는 컨테이너
* 목적: `null` 대신 사용해서 **NullPointerException(NPE) 방지**
* JPA `findById` → `Optional<Entity>` 반환

<div style="margin-top:80px;"></div>

```java
Optional<Product> found = productRepository.findById(1L);

if (found.isPresent()) {
    System.out.println("상품 있음");
} else {
    System.out.println("상품 없음");
}
```

<div style="margin-top:80px;"></div>


## 2. Optional 값 꺼내는 방식

| 메서드                     | 설명                   | 값 없음일 때                  |
| ----------------------- | -------------------- | ------------------------ |
| `get()`                 | 무조건 값 꺼냄             | `NoSuchElementException` |
| `orElse(T)`             | 값 있으면 반환, 없으면 기본값 반환 | 기본값 반환                   |
| `orElseGet(Supplier)`   | 값 없을 때만 Supplier 실행  | Supplier 결과 반환           |
| `orElseThrow(Supplier)` | 값 있으면 반환             | 지정한 예외 던짐                |

---

<div style="margin-top:80px;"></div>

## 📌 예시로 확인해보기


### 1. 타입이 `Optional<Product>`인 경우

```java
Optional<Product> found = productRepository.findById(1L);

Product product = found.orElse(new Product("기본상품"));
// 반환 타입: Product

// 👉 여기서 orElse("문자열") ❌ 불가능
// 제네릭 타입이 Product니까 문자열 넣으면 컴파일 에러
```

<div style="margin-top:80px;"></div>

### 2. 타입이 `Optional<String>`인 경우

```java
Optional<String> nameOpt = Optional.ofNullable(null);

String name = nameOpt.orElse("기본이름");
System.out.println(name); // "기본이름"

// 👉 여기서는 문자열 "기본이름"이 그대로 반환됨.
// Optional의 제네릭 타입이 String이니까 가능
```


### 📌 결론

* `orElse`에 넣는 값은 **Optional의 제네릭 타입과 같아야 함**
* JPA `findById`는 `Optional<Product>` 반환하므로

  * `orElse("문자열")` → ❌ 컴파일 에러
  * `orElse(new Product("기본상품"))` → ⭕ 올바른 방식


<div style="margin-top:80px;"></div>

## 📌 Optional 값 꺼내기 3종 세트

### 1. `orElse(T other)`

```java
String name = opt.orElse("기본값");
```

* **동작**

  * 값이 있으면 반환
  * 없으면 인자로 준 기본값 반환
* **주의점**

  * 기본값 객체 생성 로직이 항상 실행됨 (값이 있어도)
* **예시**

```java
String result = opt.orElse(getDefaultName()); 
// getDefaultName()은 값이 있어도 실행됨 ❌
```


<div style="margin-top:80px;"></div>


### 2. `orElseGet(Supplier<? extends T> supplier)`

```java
String name = opt.orElseGet(() -> "기본값");
```

* **동작**

  * 값이 있으면 반환
  * 없으면 Supplier 실행 후 반환
* **장점**

  * 지연 실행 (값이 있을 땐 supplier 실행 안 함)
* **예시**

```java
String result = opt.orElseGet(() -> getDefaultName());
// 값이 있으면 getDefaultName() 실행 ❌ (성능 최적화)
```
---

<div style="margin-top:80px;"></div>

### 3. `orElseThrow(Supplier<? extends X> exceptionSupplier)`

```java
String name = opt.orElseThrow(() -> new IllegalArgumentException("값 없음"));
```

* **동작**

  * 값이 있으면 반환
  * 없으면 지정한 예외 던짐
* **주로 사용**

  * 반드시 값이 있어야 할 때 (없으면 로직상 에러)
* **예시**

```java
Product p = productRepository.findById(id)
    .orElseThrow(() -> new NoSuchElementException("해당 상품 없음"));
```

---


## 📌 차이 비교표

| 메서드           | 값 있음 | 값 없음             | 특징                   |
| ------------- | ---- | ---------------- | -------------------- |
| `orElse`      | 값 반환 | 기본값 반환           | 기본값 **항상 실행**        |
| `orElseGet`   | 값 반환 | Supplier 실행 후 반환 | 기본값 생성 **필요할 때만 실행** |
| `orElseThrow` | 값 반환 | 예외 던짐            | 필수값일 때 적합            |

---

<div style="margin-top:80px;"></div>

# ✅ 최종 정리

* `Optional`은 null 안전하게 값 존재 여부를 표현하는 컨테이너
* **타입에 맞는 기본값 제공**: `orElse` / `orElseGet`
* **반드시 있어야 하는 값**: `orElseThrow`
* **실무에서는**:

  * 단순 기본값 필요 시 → `orElse`
  * 무거운 객체 지연 생성 → `orElseGet`
  * 없는 경우 에러 처리 필요 → `orElseThrow`


좋은 질문이에요 👍 승현님!
`Optional`은 생각보다 메서드가 다양해서, 단순히 `orElse` / `orElseThrow`만 쓰는 게 아니고 **값 존재 여부 확인 → 값 꺼내기 → 값 변환 → 조건 처리** 등 체계적으로 나뉘어 있어요.
제가 실무에서 많이 쓰는 기준으로 **카테고리별 정리**를 해드릴게요.


<div style="margin-top:80px;"></div>

# 📌 Optional 주요 메서드 정리

<div style="margin-top:80px;"></div>

## 1. 생성 관련

```java
Optional.of(value)         // 값이 null이면 NPE 발생
Optional.ofNullable(value) // 값이 null이면 Optional.empty()
Optional.empty()           // 비어 있는 Optional
```

<div style="margin-top:80px;"></div>

## 2. 값 존재 여부 확인

```java
isPresent() // 값 있으면 true
isEmpty()   // 값 없으면 true (Java 11+)
```


<div style="margin-top:80px;"></div>

## 3. 값 꺼내기

```java
get()                        // 값 꺼내기 (없으면 예외 발생 ❌ 남용 금지)
orElse(T other)              // 값 없으면 기본값 반환
orElseGet(Supplier)          // 값 없으면 Supplier 실행
orElseThrow(Supplier)        // 값 없으면 예외 던짐
```

<div style="margin-top:80px;"></div>

## 4. 조건 처리

```java
ifPresent(Consumer)          // 값 있으면 실행
ifPresentOrElse(Consumer, Runnable) // 값 있으면 Consumer, 없으면 Runnable 실행 (Java 9+)
```

<div style="margin-top:80px;"></div>


## 5. 값 변환 (스트림처럼 활용 가능)

```java
map(Function)                // 값이 있으면 함수 적용 후 Optional 반환
flatMap(Function)            // map과 같지만 결과가 Optional이면 중첩 제거
filter(Predicate)            // 조건 만족 시 Optional 유지, 아니면 empty
```


<div style="margin-top:80px;"></div>


## 6. 스트림 변환 (Java 9+)

```java
stream() // Optional을 Stream으로 변환 → 비어있으면 빈 스트림
```



<div style="margin-top:80px;"></div>

# 📌 메서드 카테고리별 표 정리

| 카테고리  | 메서드                                         | 설명                  |
| ----- | ------------------------------------------- | ------------------- |
| 생성    | `of`, `ofNullable`, `empty`                 | Optional 객체 생성      |
| 확인    | `isPresent`, `isEmpty`                      | 값 존재 여부 확인          |
| 꺼내기   | `get`, `orElse`, `orElseGet`, `orElseThrow` | 값 꺼내기/대체/예외         |
| 조건 처리 | `ifPresent`, `ifPresentOrElse`              | 값이 있을 때만 실행         |
| 변환    | `map`, `flatMap`, `filter`                  | 값 가공/조건 필터링         |
| 스트림   | `stream`                                    | Stream 변환 (Java 9+) |

---

<div style="margin-top:80px;"></div>

# 📌 간단 예시 모음

```java
Optional<String> opt = Optional.ofNullable("승현");

// 값 변환
String upper = opt.map(String::toUpperCase).orElse("없음");

// 조건 필터링
opt.filter(name -> name.length() > 2)
   .ifPresent(System.out::println);

// flatMap 예시
Optional<Integer> length = opt.flatMap(s -> Optional.of(s.length()));

// 값 없으면 기본값
String name = opt.orElse("기본이름");

// 값 없으면 예외
String mustName = opt.orElseThrow(() -> new IllegalArgumentException("이름 없음"));
```

<div style="margin-top:80px;"></div>



# ✅ 결론

* **값 꺼내기**: `orElse`, `orElseGet`, `orElseThrow`
* **조건 처리**: `ifPresent`, `filter`
* **값 변환**: `map`, `flatMap`
* **최대한 `get()` 남용하지 말고** 안전한 메서드로 처리

---

👉 승현님, 원하시면 제가 이걸 **“나쁜 사용 예(get 남용) vs 좋은 사용 예(map/ifPresent 활용)”** 비교 코드로도 정리해드릴까요?
