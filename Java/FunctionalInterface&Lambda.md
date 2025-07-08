# 🧠 Java 함수형 인터페이스 & 람다 표현식 정리

> 이 프로젝트는 Java 8 이후 도입된 **함수형 인터페이스(Functional Interface)** 개념과  
> **람다 표현식(Lambda Expression)**을 통해, **전략 패턴 기반의 필터링 시스템**을 구현한다.

---

## 🧩 1. 함수형 인터페이스 (Functional Interface)

### ✔ 정의

- **추상 메서드가 단 하나만 존재하는 인터페이스**를 의미한다.
- 이 인터페이스는 람다 표현식의 타겟이 될 수 있다.
- `@FunctionalInterface` 어노테이션을 붙이면 컴파일러가 **실수 방지**를 도와준다.

```java
@FunctionalInterface
public interface ApplePredicate {
    boolean test(Apple apple);  // 반드시 1개의 추상 메서드만 존재해야 함
}
```

---

## 📚 1-1. 추상 메서드 (Abstract Method)

### ✔ 정의

- **선언만 있고 구현(몸체)은 없는 메서드**를 의미한다.
- 주로 인터페이스나 추상 클래스 내부에서 정의된다.
- 구현 클래스가 반드시 **오버라이딩(Override)** 해야 한다.

```java
public interface ApplePredicate {
    boolean test(Apple apple); // ← 이게 추상 메서드!
}
```

- 추상 메서드는 인터페이스나 추상 클래스가 **"이런 기능을 꼭 구현하라"**는 계약을 강제하는 수단이다.
- 함수형 인터페이스는 **이 추상 메서드 단 하나만 가지는 것**이 핵심이다.

---

## 🔧 2. 람다 표현식 (Lambda Expression)

### ✔ 문법

```java
(매개변수) -> { 실행문 }
```

- 반환값이 있는 경우: `x -> x * x`
- 실행문이 하나면 `{}` 생략 가능
- 타입도 컴파일러가 추론하여 생략 가능

### ✔ 기존 익명 클래스와 비교

```java
// 익명 클래스
ApplePredicate p = new ApplePredicate() {
    public boolean test(Apple a) {
        return a.getColor() == Color.GREEN;
    }
};

// 람다 표현식
ApplePredicate p = (Apple a) -> a.getColor() == Color.GREEN;
```

---

## 🍏 3. Apple 필터링 시스템 설계

### ✔ ApplePredicate 인터페이스

- 사과 객체를 전달받아 조건을 검사하는 인터페이스

```java
@FunctionalInterface
public interface ApplePredicate {
    boolean test(Apple apple);
}
```

### ✔ FilterApple 클래스

- `ApplePredicate`를 받아서 조건에 따라 사과를 필터링

```java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate predicate) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (predicate.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}
```

### ✔ 다양한 방식의 조건 전달

```java
// 1. 구현 클래스 사용
filterApples(list, new AppleWeightPredicate());

// 2. 익명 클래스 사용
filterApples(list, new ApplePredicate() {
    public boolean test(Apple a) {
        return a.getWeight() > 200;
    }
});

// 3. 람다 표현식 사용
filterApples(list, a -> a.getColor() == Color.RED);
```

---

## 🧪 4. 제네릭 함수형 인터페이스 활용

### ✔ GenericPredicate<T>

- 어떤 타입이든 필터링 가능한 범용 인터페이스

```java
@FunctionalInterface
public interface GenericPredicate<T> {
    boolean test(T t);
}
```

### ✔ GenericMapping<T>

- 특정 타입의 데이터를 가공하거나 출력하는 작업을 람다로 처리 가능

```java
@FunctionalInterface
public interface GenericMapping<T> {
    void map(T t);
}
```

---

## 🐶 5. 실전 예제: Pet 객체 필터링

```java
List<Pet> result = filter(petList, pet -> pet.getAge() > 5);
```

- `GenericPredicate<Pet>`를 통해 나이가 5세 초과인 Pet만 필터링

---

## 🔁 6. 함수형 스타일의 코드 설계 장점

| 방식               | 설명                                                           |
|--------------------|----------------------------------------------------------------|
| 전략 패턴 대체     | 다양한 조건을 매개변수로 넘기는 방식으로 **전략 패턴을 함수처럼 사용** |
| 재사용성 향상      | 공통 로직(filter/map)은 유지하고, 조건만 다르게 주입해 사용 가능         |
| 코드 간결화        | 람다 표현식 사용으로 불필요한 클래스, 중복 코드 제거               |
| 타입 안정성 확보   | 제네릭 + 함수형 인터페이스를 통해 컴파일 타임 타입 안정성 보장         |

---

## 📌 핵심 요약

| 키워드               | 설명                                                               |
|----------------------|--------------------------------------------------------------------|
| `@FunctionalInterface` | 추상 메서드 하나만 허용하는 인터페이스를 명시적으로 선언             |
| 추상 메서드          | 인터페이스가 요구하는 동작을 명시만 하고 구현은 강제하지 않는 메서드   |
| 람다 표현식          | 익명 클래스보다 간결하게 동작 정의, `(a) -> { ... }` 형태            |
| `Predicate`, `Mapping` | 조건 검사와 데이터 가공을 람다로 추상화                             |
| 전략 패턴 → 람다화   | 다양한 조건/기능을 객체가 아닌 함수로 전달하여 OOP+FP 융합 구현       |
| 활용 예시           | 사과 필터링, 펫 나이 검사 등 다양한 객체에 람다식 조건 적용 가능       |

---

