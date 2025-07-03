# 자바 상속(Inheritance) 핵심 정리

## 1. 상속의 기본 개념

-   **정의**: 부모 클래스(Super Class)가 가진 속성(필드)과 기능(메서드)을 자식 클래스(Sub Class)가 물려받는 것이다.
-   **키워드**: 자식 클래스 선언 시 `extends` 키워드를 사용한다.
-   **관계**: 상속은 **"~은 ~이다 (IS-A)"** 관계를 표현한다.
    -   예시: `Warrior`는 `Player`이다 (Warrior IS-A Player).
-   **최상위 클래스**: 모든 클래스는 명시적으로 상속받지 않으면, 자동으로 `Object` 클래스를 상속받는다.

```java
/* Player를 상속받는 Warrior 클래스 */
public class Warrior extends Player {
    // ...
}

/* 명시적 상속이 없으므로 Object를 상속받는 Player 클래스 */
public class Player extends Object { // extends Object는 생략 가능
    // ...
}
```

# 📘 생성자와 `super()` 호출 규칙 정리

## 1. 생성자 호출 규칙
- 자식 클래스의 생성자는 **반드시 부모 클래스의 생성자**를 호출해야 함.
- 이 호출은 **`super()`** 키워드를 통해 이루어짐.

## 2. `super()` 키워드란?
- 부모 클래스의 생성자를 호출하는 키워드.
- 자식 클래스의 생성자에서 `super()`를 사용해 **부모 클래스의 생성자 로직을 실행**시킬 수 있음.

## 3. 자동 호출 규칙
- 자식 클래스의 생성자에서 `super()` 호출을 생략하면,
  > 컴파일러가 **부모의 기본 생성자 (`super();`)를 자동으로 첫 줄에 추가**함.

```java
class Parent {
    Parent() {
        System.out.println("부모 생성자");
    }
}

class Child extends Parent {
    Child() {
        // super(); ← 컴파일러가 자동 추가
        System.out.println("자식 생성자");
    }
}
```
## 4. 주의사항 ⚠️
* 부모 클래스에 기본 생성자(파라미터 없는 생성자)가 없고, 파라미터가 있는 생성자만 있을 경우,

* 자식 클래스에서 super()를 통해 명시적으로 어떤 부모 생성자를 호출할지 지정해줘야 함.

* 그렇지 않으면 컴파일 에러 발생!

```java
class Parent {
    Parent(String name) {
        System.out.println("부모 생성자: " + name);
    }
}

class Child extends Parent {
    Child() {
        // super(); ← 오류! 기본 생성자가 없음
        super("자식 이름"); // ✅ 명시적으로 부모 생성자 호출
        System.out.println("자식 생성자");
    }
}
```


# 🔁 메서드 오버라이딩 (Method Overriding)

## 1. 정의
- **오버라이딩이란?**
  > 부모 클래스에서 상속받은 메서드를 **자식 클래스의 목적에 맞게 재정의**하는 것.

---

## 2. 오버라이딩 규칙 ✅

| 항목            | 설명 |
|-----------------|------|
| 메서드 시그니처 | 부모와 **이름, 리턴 타입, 파라미터**가 완전히 같아야 함 |
| 접근 제한자     | 부모보다 **같거나 더 넓은 범위**여야 함 <br>예: 부모가 `default`면, 자식은 `default` 또는 `public` 가능 |
| 예외 처리 범위  | 부모보다 **같거나 더 좁은 예외**만 throws 가능 |

```java
class Parent {
    void greet() {
        System.out.println("안녕!");
    }
}

class Child extends Parent {
    @Override
    public void greet() {  // ✅ 메서드 이름, 파라미터, 리턴타입 모두 동일
        System.out.println("Hello!");
    }
}
```

## 3. @Override

* 오버라이딩을 명시적으로 선언하는 키워드

* 작성하지 않아도 작동하지만, 오타나 규칙 위반 시 컴파일 에러를 발생시켜 실수를 방지할 수 있음

```java
@Override
void greet() {
    // 오버라이딩된 메서드
}
```

## 4. super 키워드 활용
* 오버라이딩한 메서드 안에서 부모의 원래 메서드를 호출할 수 있음

```java
@Override
public void greet() {
    super.greet(); // 부모 메서드 호출
    System.out.println("Hi!");
}
```
```java
// 자식 클래스 Warrior에서 부모의 showStatus()를 오버라이딩
public class Warrior extends Player {

    int rage; // 자식 고유의 속성

    @Override // 오버라이딩 규칙을 검사
    void showStatus() {
        super.showStatus(); // 부모의 showStatus()를 먼저 호출하여 기존 정보 출력
        System.out.println("# 분노: " + this.rage); // 자식의 고유 정보 추가 출력
    }
}
```



## 4. 메서드 오버로딩 (Method Overloading)
* 정의: 
  * 오버라이딩과 달리, 하나의 클래스 내에서 이름이 같은 메서드를 여러 개 정의하는 것이다.
* 규칙: 
  * 메서드 이름은 같지만, 파라미터의 (1)개수, (2)타입, (3)순서 중 하나 이상이 달라야 한다. 
  * 리턴 타입이나 접근 제한자는 오버로딩 성립에 영향을 주지 않는다.


```java
  // Example 클래스 내의 오버로딩 예시
public class Example {
    void add() {}
    void add(int x) {}          // 파라미터 개수가 다름
    void add(double x) {}       // 파라미터 타입이 다름
    void add(int x, int y) {}   // 파라미터 개수가 다름
    void add(int x, double y) {}// 파라미터 타입이 다름
    void add(double x, int y) {}// 파라미터 순서가 다름
}
```