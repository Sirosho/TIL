# Java 컬렉션 프레임워크: Map 요점 정리

<div style="margin-top:80px;"></div>

## 1. Map 인터페이스의 특징

- `Map`은 **Key**와 **Value**가 하나의 쌍(Entry)으로 묶여 저장되는 자료구조이다.
- JSON 객체나 파이썬의 딕셔너리(Dictionary)와 유사한 구조를 가진다. 그래서 JSON을 다룰때 많이 쓴다.
- **Key는 중복을 허용하지 않는다.** 만약 중복된 Key로 값을 저장하면, 기존의 값은 새로운 값으로 덮어쓰여 수정된다.
- **Value는 중복을 허용한다.**
- `HashMap`과 같은 대부분의 구현체는 **저장 순서를 보장하지 않는다.**

<div style="margin-top:80px;"></div>

## 2. HashMap 클래스

- `Map` 인터페이스의 가장 대표적인 구현 클래스이다.
- 내부적으로 Key를 **해시(Hash)**하여 값을 저장하므로, 저장 순서가 유지되지 않는다.
- Key를 이용한 데이터 검색, 추가, 삭제 속도가 매우 빠르다.

```java
// 제네릭(Generic)을 사용한 현대적인 방식 (타입 안정성 보장)
Map<String, Object> map = new HashMap<>();

// 예제 코드의 방식 (제네릭 미사용, 하위 호환성을 위해 존재)
// get()으로 값을 꺼낼 때마다 형 변환이 필요하다.
Map rawMap = new HashMap();
```


<div style="margin-top:80px;"></div>

## 3. HashMap 주요 메서드 기능

| 메서드                     | 기능 설명                                                                                                                                              | 사용 예시                                   |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------- |
| `put(K key, V value)`      | 지정된 Key에 Value를 매핑하여 저장한다. 만약 Key가 이미 존재하면, 기존의 Value를 새로운 Value로 덮어쓰고(수정) 이전 값을 반환한다.                      | `map.put("empName", "김철수");`           |
| `get(Object key)`          | 지정된 Key에 매핑된 Value를 반환한다. Key가 존재하지 않으면 `null`을 반환한다. 제네릭을 사용하지 않은 경우, 반환 타입이 `Object`이므로 적절한 타입으로 형 변환(casting)이 필요하다. | `String name = (String)map.get("empName");` |
| `size()`                   | Map에 저장된 Key-Value 쌍(엔트리)의 총 개수를 반환한다.                                                                                                | `int count = map.size();`                   |
| `containsKey(Object key)`  | Map에 지정된 Key가 존재하는지 여부를 확인한다. `boolean` 값을 반환한다.                                                                                | `boolean hasKey = map.containsKey("empName");` |
| `remove(Object key)`       | 지정된 Key와 그에 매핑된 Value를 Map에서 삭제한다.                                                                                                     | `map.remove("salary");`                    |
| `keySet()`                 | Map에 포함된 모든 Key를 `Set` 형태로 반환한다. Map의 모든 요소를 순회할 때 주로 사용된다.                                                                | `Set<String> keys = map.keySet();`          |

<div style="margin-top:80px;"></div>

## 4. Map의 반복문 처리 (순회)

`Map`은 인덱스가 없기 때문에 일반 `for`문으로 순회할 수 없다. 대신 `keySet()` 메서드를 활용하는 것이 일반적인 방법이다.

1.  `keySet()` 메서드를 호출하여 모든 Key가 담긴 `Set`을 얻는다.
2.  향상된 `for`문을 사용하여 `Set`의 각 Key를 순회한다.
3.  루프 내부에서 `get(key)` 메서드를 사용하여 현재 Key에 해당하는 Value를 조회한다.

```java
// 1. 모든 키를 Set으로 가져온다.
Set<String> keys = map.keySet();

// 2. 키의 Set을 반복 처리한다.
for (String key : keys) {
    // 3. 키를 이용해 값을 가져온다.
    Object value = map.get(key);
    System.out.printf("Key: %s, Value: %s\n", key, value);
}
```