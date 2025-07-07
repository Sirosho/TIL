# Java 컬렉션 프레임워크: Set 요점 정리

<div style="margin-top:80px;"></div>


## 1. Set 인터페이스의 특징

- `Set`은 **데이터의 중복 저장을 허용하지 않는** 데이터의 집합(Set) 자료구조이다.
- 대부분의 `Set` 구현체는 **저장 순서를 보장하지 않는다.**
- `List`와 달리 인덱스(index)가 존재하지 않으므로, 순차적인 데이터 접근이 불가능하다.
- 순서를 고려하지 않고 해시(Hash)를 기반으로 데이터를 관리하므로, 일반적으로 `List`보다 데이터의 추가, 검색, 삭제 속도가 빠르다.

<div style="margin-top:80px;"></div>

## 2. HashSet 클래스

- `Set` 인터페이스의 가장 대표적인 구현 클래스이다.
- 내부적으로 **해시(Hash) 알고리즘**을 사용하여 데이터를 관리한다.
- 이로 인해 데이터의 검색, 추가, 삭제 속도가 매우 빠르지만, 저장 순서는 유지되지 않는다.

```java
// HashSet 클래스로 Set 객체 생성
Set<String> set = new HashSet<>();
```

<div style="margin-top:80px;"></div>


## 3. HashSet 주요 메서드 기능

| 메서드                                     | 기능 설명                                                                                                                                                                                          | 사용 예시                                 |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| `add(E element)`                           | `Set`에 데이터를 추가한다. 이미 동일한 데이터가 존재하면 추가되지 않고 `false`를 반환하며, 성공적으로 추가되면 `true`를 반환한다.                                                                      | `boolean flag = set.add("김밥");`         |
| `remove(Object o)`                         | 지정된 데이터를 `Set`에서 삭제한다. 인덱스가 없으므로 객체를 직접 지정해야 한다.                                                                                                                   | `set.remove("단무지");`                     |
| `size()`                                   | `Set`에 저장된 데이터의 총 개수를 반환한다.                                                                                                                                                        | `int size = set.size();`                  |
| `new HashSet<>(Collection<? extends E> c)` | 다른 컬렉션 객체를 받아, 해당 컬렉션의 모든 요소를 포함하는 새로운 `Set`을 생성한다. 이 과정에서 **중복된 요소는 자동으로 제거된다.**                                                                   | `new HashSet<>(numbersList);`            |


<div style="margin-top:80px;"></div>

## 4. Set의 주요 활용: 리스트(List)의 중복 데이터 제거

`Set`의 '중복 불가' 특징은 `List`와 같은 다른 컬렉션의 중복 데이터를 손쉽게 제거하는 용도로 매우 유용하게 사용된다.

1.  **중복 데이터가 포함된 `List`를 준비한다.**
    ```java
    List<Integer> numbers = List.of(3, 3, 1, 2, 3, 4, 5, 6, 7, 8, 1, 1);
    // [3, 3, 1, 2, 3, 4, 5, 6, 7, 8, 1, 1]
    ```

2.  **해당 `List`를 `HashSet` 생성자에 전달하여 `Set`으로 변환한다.**
    이 과정에서 중복된 값들은 자동으로 제거된다.
    ```java
    Set<Integer> numSet = new HashSet<>(numbers);
    // [1, 2, 3, 4, 5, 6, 7, 8] (순서는 보장되지 않음)
    ```

3.  **필요 시, 중복이 제거된 `Set`을 다시 `List`로 변환한다.**
    ```java
    ArrayList<Integer> uniqueNumbers = new ArrayList<>(numSet);
    // [1, 2, 3, 4, 5, 6, 7, 8]
    ```

    <div style="margin-top:80px;"></div>

## 5. 사용자 정의 객체(Custom Object)와 Set

`Set`에 사용자 정의 객체를 저장하고, 객체의 내용(필드 값)을 기준으로 중복을 판별하려면 **반드시 `equals()`와 `hashCode()` 메서드를 재정의(Override)해야 한다.**

- **기본 동작**: 재정의하지 않으면, 두 객체의 메모리 주소값(`==` 비교)을 기준으로 동일성을 판단한다. 따라서 필드 값이 같더라도 서로 다른 객체로 인식하여 중복 저장을 막지 못한다.

- **`equals(Object obj)` 재정의**: 객체의 "논리적 동등성"(예: 사람의 주민번호가 같으면 같은 사람)을 비교하는 규칙을 정의한다.

- **`hashCode()` 재정의**: `HashSet`은 `hashCode()`가 반환하는 정수 값(해시 코드)을 사용하여 데이터를 저장할 위치를 결정하고, 중복 여부를 빠르게 1차적으로 판단한다.
    - **규칙**: `equals()`가 `true`를 반환하는 두 객체는 반드시 동일한 `hashCode()` 값을 반환해야 한다.

    <div style="margin-top:80px;"></div>

### 예시: Person 클래스
아래 `Person` 클래스는 `equals()`와 `hashCode()`가 재정의되지 않았다. 따라서 `p1`과 `p3`는 필드 값이 모두 같음에도 불구하고 `HashSet`에 모두 추가된다.

```java
class Person {
    String name;
    String ssn; // 주민번호

    public Person(String name, String ssn) {
        this.name = name;
        this.ssn = ssn;
    }
    
    // Set에서 'ssn'을 기준으로 중복을 제거하려면
    // equals()와 hashCode()를 ssn 필드 기반으로 재정의해야 한다.
}

// 사용 코드
Person p1 = new Person("김철수", "850516");
Person p3 = new Person("김철수", "850516");

Set<Person> personSet = new HashSet<>();
personSet.add(p1);
personSet.add(p3);

// personSet의 크기는 2가 된다. (p1과 p3를 다른 객체로 인식)
System.out.println(personSet); 
```


<div style="margin-top:80px;"></div>


# equals()와 hashCode()를 오버라이딩해야 하는 이유

자바에서 equals()와 hashCode() 메서드는 객체 비교 및 해시 기반 컬렉션의 동작에 핵심적인 역할을 담당한다. 두 메서드를 올바르게 오버라이딩하지 않으면 논리적으로 같은 객체임에도 불구하고 컬렉션에서 다르게 취급되는 문제가 발생한다. 아래에 그 구체적인 이유를 정리한다.

---

<div style="margin-top:80px;"></div>

## 1. 기본 equals()와 hashCode()는 주소 기반 비교이다

- Object 클래스의 equals()는 `this == obj`로 구현되어 있다.
- hashCode()는 native 메서드이며, JVM이 객체의 메모리 주소나 내부 식별값을 기반으로 해시값을 계산한다.
- 따라서 두 객체가 같은 값을 갖고 있어도, 다른 메모리 주소를 가질 경우 equals()는 false를 반환하고 hashCode()도 다르게 나온다.

---

<div style="margin-top:80px;"></div>

## 2. hash 기반 컬렉션에서 정상적인 동작을 보장하기 위함이다

### 2-1. HashSet, HashMap, Hashtable 등
- 이들 컬렉션은 내부적으로 객체의 hashCode()를 기준으로 데이터를 빠르게 검색하고, equals()를 통해 중복 여부를 판단한다.
- hashCode()가 다르면 다른 버킷에 저장되어, equals()가 true여도 중복을 막지 못한다.
- equals()가 false이면 논리적으로 동일한 객체라도 중복된 값으로 취급된다.

### 2-2. Set.contains(), Map.get(), List.remove(Object)
- Set.contains() → hashCode() + equals() 필요
- Map.get(key) → key의 hashCode() + equals() 필요
- List.remove(Object) → equals()만 필요

---

<div style="margin-top:80px;"></div>

## 3. ORM 및 프레임워크에서 정확한 객체 비교를 위해 필요하다

### 3-1. JPA Entity
- 엔티티의 비교나 1차 캐시 처리, 컬렉션 매핑(Set 등)에서 equals/hashCode가 정확히 정의되어 있어야 한다.
- 보통 식별자 필드(id)만 기준으로 오버라이딩하는 것이 안전하다.

### 3-2. 테스트 코드 (JUnit, AssertJ 등)
- `assertEquals(obj1, obj2)`는 equals() 기준으로 비교되므로 정확한 비교를 위해 오버라이딩이 필요하다.

---

<div style="margin-top:80px;"></div>

## 4. 오버라이딩 시 유의사항

- equals()를 오버라이딩하면 반드시 hashCode()도 함께 오버라이딩해야 한다.
- equals()가 true를 반환하는 두 객체는 반드시 동일한 hashCode() 값을 가져야 한다.
- hashCode()만 오버라이딩하고 equals()를 오버라이딩하지 않으면 논리적 오류가 발생할 수 있다.

---

<div style="margin-top:80px;"></div>

## 5. 대안

- Java 16부터 제공되는 `record`는 equals()와 hashCode()를 자동 생성해준다.
- Lombok의 `@EqualsAndHashCode`를 통해 자동 생성할 수 있다.
- IDE 자동 생성 기능(IntelliJ 기준 Alt + Insert → equals and hashCode) 활용 가능하다.

---

<div style="margin-top:80px;"></div>

## 결론

equals()와 hashCode()의 올바른 오버라이딩은 자바 객체를 논리적으로 비교하거나, 해시 기반 자료구조에서의 정확한 동작을 보장하기 위한 필수 요소이다. 사용자 정의 클래스를 HashSet, HashMap, JPA Entity, 테스트 비교 등 다양한 용도로 사용할 경우 반드시 equals()와 hashCode()의 오버라이딩 여부를 고려해야 한다.
