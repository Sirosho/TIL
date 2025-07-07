# Java 컬렉션 프레임워크: List

<div style="margin-top:80px;"></div>

## 1. List 인터페이스의 특징

- `List`는 **순서가 있는** 데이터의 집합이며, 데이터의 **중복 저장을 허용**한다.
- 내부적으로 인덱스(index)를 사용하여 데이터를 관리하므로, JSON 배열과 유사한 구조를 가진다.
- `List`는 인터페이스이므로, `ArrayList`, `LinkedList` 등의 구현 클래스를 통해 객체를 생성해야 한다.

```java
// List 인터페이스를 ArrayList 구현 클래스로 생성 (다형성)
List<String> list = new ArrayList<>();
```


<div style="margin-top:80px;"></div>

## 2. List의 주요 구현 클래스

### 2.1. ArrayList (배열 리스트)

- 내부적으로 배열(Array)을 사용하여 데이터를 관리하는 클래스이다.
- 데이터들이 메모리상에 일렬로 붙어있는 연속적인 구조를 가진다.

- **장점**:
    - 데이터가 메모리에 연속적으로 위치하여 전체 탐색 속도가 빠르다.
    - 인덱스를 통한 데이터 접근(Random Access)이 매우 빠르다. (O(1))

- **단점**:
    - 데이터의 추가, 삽입, 삭제 시 배열의 재할당 및 복사가 발생할 수 있어 비효율적이다. (특히 리스트 중간에 데이터를 삽입/삭제할 경우)

- **사용 경우**:
    - 데이터 조회가 빈번하고, 데이터의 변경(추가/삭제)이 적은 경우에 사용하는 것이 유리하다.
    - 읽기 전용(Read-only) 데이터를 다룰 때 효과적이다.

### 2.2. LinkedList (연결 리스트)

- 데이터와 주소값으로 이루어진 노드(Node)들이 연쇄적으로 연결된 형태의 구조이다. (ex: `1 -> 2 -> 3`)

- **장점**:
    - 각 노드의 주소값만 변경하면 되므로 데이터의 삽입 및 삭제가 매우 효율적이다.

- **단점**:
    - 특정 데이터를 찾기 위해 첫 데이터부터 순차적으로 탐색(Sequential Access)해야 하므로 탐색 속도가 느리다.

- **사용 경우**:
    - 데이터의 추가, 삽입, 삭제가 빈번하게 발생하는 경우에 사용하는 것이 유리하다.

    <div style="margin-top:80px;"></div>

## 3. ArrayList 주요 메서드 기능

| 메서드                                     | 기능 설명                                                                                                                                | 사용 예시                          |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- |
| `add(E element)`                           | 리스트의 맨 끝에 새로운 데이터를 추가한다.                                                                                               | `hobbies.add("취미1");`          |
| `size()`                                   | 리스트에 저장된 데이터의 총 개수를 반환한다.                                                                                             | `int size = hobbies.size();`     |
| `get(int index)`                           | 지정된 인덱스에 위치한 데이터를 반환(참조)한다.                                                                                          | `String hobby = hobbies.get(1);` |
| `set(int index, E element)`                | 지정된 인덱스의 데이터를 새로운 데이터로 교체(수정)한다.                                                                                 | `hobbies.set(1, "디자인");`      |
| `indexOf(Object o)`                        | 지정된 데이터가 리스트에서 처음으로 발견되는 인덱스를 반환한다. 데이터가 없으면 -1을 반환한다.                                            | `int index = hobbies.indexOf("취미2");` |
| `remove(int index)`                        | 지정된 인덱스의 데이터를 삭제한다.                                                                                                       | `numbers.remove(1);`             |
| `clear()`                                  | 리스트의 모든 데이터를 삭제한다.                                                                                                         | `hobbies.clear();`               |
| `isEmpty()`                                | 리스트가 비어있는지 여부를 확인한다. 비어있으면 `true`, 데이터가 하나라도 있으면 `false`를 반환한다.                                       | `boolean b = hobbies.isEmpty();` |
| `List.of(E... elements)`                   | (Java 9+) 여러 개의 초기 데이터를 포함하는 **불변(Immutable) 리스트**를 생성한다. 이 리스트는 생성 후 수정(추가, 삭제, 변경)이 불가능하다. | `List.of(1, 3, 5);`              |
| `new ArrayList<>(Collection<? extends E> c)` | 다른 컬렉션의 모든 요소를 포함하는 **가변(Mutable) 리스트**를 생성한다. 불변 리스트를 수정 가능한 리스트로 만들 때 주로 사용된다.             | `new ArrayList<>(List.of(1, 3, 5));` |


<div style="margin-top:80px;"></div>

## 4. 리스트 순회 (Iteration)

리스트에 저장된 모든 데이터를 순서대로 접근하는 방법은 다음과 같이 3가지가 주로 사용된다.

### 4.1. 일반 for문
```java
for (int i = 0; i < hobbies.size(); i++) {
    System.out.println(hobbies.get(i));
}
```

### 4.2. 향상된 for문 (Enhanced for-loop)
`List`가 `Iterable` 인터페이스를 구현하기 때문에 사용 가능하다.
```java
for (String h : hobbies) {
    System.out.println(h);
}
```

### 4.3. forEach 메서드와 람다 표현식
```java
hobbies.forEach(h -> System.out.println(h));
```


<div style="margin-top:80px;"></div>



# 자바 컬렉션의 순서 보장 여부 정리

---

## ✅ List 인터페이스의 특징

- 인덱스(index) 기반으로 요소에 접근할 수 있음  
- 삽입 순서 유지  
- 중복 허용  

---

<div style="margin-top:80px;"></div>

## 🔹 대표적인 List 구현체

| 자료구조 | 순서 보장 | 설명 |
|----------|-----------|------|
| `ArrayList` | ✅ 유지됨 | 배열 기반, 읽기 빠름 |
| `LinkedList` | ✅ 유지됨 | 연결 리스트 기반, 삽입/삭제 빠름 |
| `Vector` | ✅ 유지됨 | ArrayList + 동기화 |
| `Stack` (Vector 상속) | ✅ 유지됨 | 후입선출(LIFO) 구조 |

- 이들 모두 삽입한 순서대로 요소가 저장된다.
- `get(index)`, `add(index, element)`, `remove(index)` 등의 위치 기반 연산이 가능하다.

---

<div style="margin-top:80px;"></div>

## 🔸 반대로 순서를 보장하지 않는 대표 인터페이스들

| 인터페이스 | 순서 보장 여부 | 예시 구현체 |
|------------|----------------|-------------|
| `Set` | ❌ 기본은 보장 안 함 | `HashSet`, `TreeSet` |
| `Map` | ❌ 기본은 보장 안 함 | `HashMap`, `Hashtable` |

---

<div style="margin-top:80px;"></div>

## 🔸 예외적으로 순서를 보장하는 Set, Map 구현체

| 자료구조 | 순서 보장 방식 | 특징 |
|----------|----------------|------|
| `LinkedHashSet` | 입력 순서 | 순서를 유지하면서 중복 없는 Set |
| `TreeSet` | 정렬 순서 | `Comparable` 또는 `Comparator` 기준으로 자동 정렬 |
| `LinkedHashMap` | 입력 순서 | 삽입 순서를 유지하는 Map |
| `TreeMap` | 정렬 순서 | Key 값 기준 정렬 (`Comparable` 또는 `Comparator`) |

---

<div style="margin-top:80px;"></div>

## 🧠 결론

- **순서를 유지하려면 `List` 또는 `LinkedHash*`, `Tree*` 계열 사용을 고려한다.**
- 일반적인 `HashSet`, `HashMap`은 **순서가 없으므로**, 순서가 중요한 경우 주의가 필요하다.
