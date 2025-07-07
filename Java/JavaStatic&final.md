# 자바의 static 키워드: 정적 멤버와 인스턴스 멤버

## 1. 개요

 * `static`은 멤버(필드, 메서드)를 개별 객체(인스턴스)가 아닌, 클래스 자체에 소속되도록 지정하는 **사용 제한자(usage modifier)** 
 * 이를 통해 모든 객체가 데이터를 공유하거나, 객체 생성 없이 특정 기능을 사용할 수 있게 된다.

---

## 2. `static` 키워드의 핵심 특징

### 2.1. 메모리 영역과 생명주기

-   **인스턴스 멤버 (non-static)**:
    -   객체가 생성될 때(`new Count()`) **힙(Heap) 영역**에 할당된다.
    -   각 객체마다 별도의 공간을 가지므로, 서로 다른 값을 유지한다.
    -   객체가 가비지 컬렉터에 의해 소멸될 때 함께 사라진다.

-   **정적 멤버 (static)**:
    -   클래스가 JVM에 의해 로딩될 때 **메서드 영역(Method Area, 또는 Data Area)**에 **단 한 번** 할당된다.
    -   해당 클래스의 모든 객체가 이 영역을 **공유**한다.
    -   프로그램이 종료될 때까지 메모리에 상주한다.

### 2.2. 정적 필드 (Static Field)

-   **`공유 변수`**로서의 역할을 한다.
-   한 객체에서 정적 필드의 값을 변경하면, 다른 모든 객체에서도 변경된 값을 참조하게 된다.
-   **접근 방식**: 클래스 이름을 통해 직접 접근하는 것이 권장된다. (`클래스명.필드명`)

#### 코드 예시: `Count.java`와 `Main.java`
```java
public class Count {
    public static int x; // 정적 필드 (공유됨)
    public int y;        // 인스턴스 필드 (객체별로 생성됨)
}

// Main.java 에서의 실행
Count c1 = new Count();
Count c2 = new Count();
// x는 static
c1.y = 10;
c2.y = 20;
System.out.println(c1.y); // 출력: 10
System.out.println(c2.y); // 출력: 20 (y는 객체마다 독립적)

Count.x = 100; // 클래스 이름으로 접근
System.out.println(c1.x); // 출력: 100
System.out.println(c2.x); // 출력: 100 (x는 모든 객체가 공유)

c2.x = 999;
System.out.println(c1.x); // 출력: 999 (c2에서 바꿔도 c1에 영향을 줌)
```

### 2.3. 정적 메서드 (Static Method)

-   객체를 생성하지 않고, **클래스 이름으로 직접 호출**할 수 있는 메서드이다.
-   주로 객체의 상태(인스턴스 필드)와 관계없이 독립적으로 수행할 수 있는 기능에 사용된다. (e.g., 유틸리티 메서드)

#### 정적 메서드 내부의 규칙

1.  **`this` 참조 사용 불가**: `this`는 현재 실행 중인 "객체 자신"을 의미하는데, 정적 메서드는 특정 객체에 소속된 것이 아니므로 `this`를 사용할 수 없다.
2.  **인스턴스 멤버 접근 불가**: `this`를 사용할 수 없으므로, 인스턴스 필드나 인스턴스 메서드를 직접 호출할 수 없다. 접근하려면 메서드 외부에서 객체를 생성하여 그 참조를 전달받아야 한다.
3.  **정적 멤버만 직접 접근 가능**: 정적 메서드 내부에서는 다른 정적 필드나 정적 메서드만 직접 호출할 수 있다.

#### 코드 예시: `Count.java`

```java
public class Count {
    public static int x;
    public int y;

    // 정적 메서드
    public static void m1() {
        System.out.println("static method call!!");
        x = 100; // OK: 정적 필드 접근 가능
        // y = 200; // Error: 인스턴스 필드 직접 접근 불가
        // m2();    // Error: 인스턴스 메서드 직접 접근 불가
    }

    // 인스턴스 메서드
    public void m2() {
        System.out.println("instance method call!!");
        x = 100; // OK: 인스턴스 메서드는 정적 필드 접근 가능
        y = 200; // OK: 인스턴스 필드 접근 가능
        m1();    // OK: 인스턴스 메서드는 정적 메서드 호출 가능
    }
}
```
---

## 3. `static`의 주요 활용 사례

### 3.1. 공유 데이터 (상수 또는 공용 변수)

-   모든 객체가 공통적으로 사용해야 하는 값에 `static`을 적용한다.
-   `final`과 함께 사용되어 불변의 **상수(Constant)**를 정의하는 경우가 많다.

#### 코드 예시: `Calculator.java`
```java
public class Calculator {
    // 원주율(pi)은 모든 계산기 객체가 동일한 값을 가져야 하므로 static으로 선언
    static double pi = 3.14159; 
}
```

### 3.2. 유틸리티성 메서드

-   객체의 상태와 무관하게, 입력값만으로 결과를 도출할 수 있는 기능적 메서드들에 사용된다.
-   `Math` 클래스의 모든 메서드(`Math.random()`, `Math.abs()` 등)가 대표적인 예시이다.

#### 코드 예시: `Calculator.java`
```java
public class Calculator {
    String color; // 인스턴스 필드
    static double pi = 3.14159; // 정적 필드

    // 계산기 색상은 객체마다 다르므로 인스턴스 메서드
    void customizeColor(String newColor) {
        this.color = newColor;
    }

    // 원의 넓이를 구하는 기능은 반지름(r)과 pi값만 있으면 된다.
    // 특정 계산기 객체의 색상이나 다른 상태가 필요 없으므로 static 메서드로 적합하다.
    static double calcCircleArea(double r) {
        return pi * r * r;
    }
}
```
---

## 4. 요약: 언제 `static`을 사용해야 하는가?

-   **필드**: "이 데이터가 모든 객체에서 **공통적**으로 사용되는가?"
    -   Yes: `static`을 사용한다. (e.g., 생성된 객체의 총 개수, 상수)
    -   No: 인스턴스 필드로 둔다. (e.g., 자동차의 색상, 사람의 이름)

-   **메서드**: "이 기능을 수행할 때, **객체의 고유한 상태(인스턴스 필드)가 필요한가**?"
    -   No: `static` 메서드로 만든다. (e.g., 두 수를 더하는 기능)
    -   Yes: 인스턴스 메서드로 만든다. (e.g., 자동차의 속도를 올리는 기능)


  

# final : 불변성과 확장 제어

## 1. 개요

`final`은 "최종적인", "변경할 수 없는"의 의미를 지닌 **사용 제한자(usage modifier)**이다. 변수에 적용하면 **불변성(Immutability)**을 부여하고, 메서드나 클래스에 적용하면 **확장(상속/오버라이딩)을 제한**한다. 이를 통해 코드의 안정성과 예측 가능성을 높일 수 있다.

---

## 2. `final` 필드: 불변의 데이터

`final`로 선언된 필드는 **값을 단 한 번만 할당**할 수 있다. 초기화 이후에는 값을 변경할 수 없다.

### 2.1. 인스턴스 `final` 필드 (객체별 불변 값)

-   객체마다 다른 값을 가지면서도, 생성된 이후에는 그 값이 절대 변하지 않도록 보장한다.
-   **초기화 방법**:
    1.  선언과 동시에 초기화
    2.  **생성자**를 통해 초기화 (객체별로 다른 값을 부여할 때 주로 사용)

#### 코드 예시: `Student` 클래스의 `ssn`
```java
public class Student {
    public final String ssn; // 주민번호

    // 생성자를 통해 ssn 필드를 단 한 번 초기화한다.
    public Student(String name, String ssn) {
        this.name = name;
        this.ssn = ssn;
    }
}

// Main.java 에서의 사용
Student st1 = new Student("김철수", "991123-1234567");
// st1.ssn = "다른값"; // 컴파일 에러! 생성 후 재할당 불가
```

### 2.2. `static final` 필드 (클래스 상수, Constant)

-   **자바의 상수(Constant)**는 **`static`**과 **`final`**을 함께 사용하여 정의한다.
    -   `static` (유일성): 모든 객체가 공유하는 단 하나의 값.
    -   `final` (불변성): 한 번 정해지면 절대 바뀌지 않는 값.
-   **초기화 방법**:
    1.  선언과 동시에 초기화
    2.  **정적 초기화자(Static Initializer)** `static { ... }` 블록을 사용.
        -   클래스가 메모리에 로딩될 때 최초 한 번만 실행되며, 복잡한 초기화 로직이 필요할 때 유용하다.

#### 코드 예시: `Student` 클래스의 `NATION`
```java
public class Student {
    public static final String NATION;

    // 정적 초기화자: Student 클래스가 처음 사용될 때 단 한 번 호출됨
    static {
        System.out.println("정적 초기화자 호출!");
        NATION = "대한민국";
    }
}
```

---

## 3. `final` 메서드와 클래스: 확장 제한

### 3.1. `final` 메서드 (오버라이딩 금지)

-   자식 클래스에서 해당 메서드를 **재정의(Overriding)할 수 없다.**
-   클래스의 핵심 동작이 자식에 의해 임의로 변경되는 것을 방지하여 기능의 일관성을 유지한다.

#### 코드 예시: `Animal` 클래스의 `eat`
```java
public class Animal {
    public final void eat() { // final 메서드
        System.out.println("밥을 얌냠냠~");
    }
}

class Cat extends Animal {
    // eat() 메서드를 오버라이딩하려 하면 컴파일 에러 발생
}
```

### 3.2. `final` 클래스 (상속 금지)

-   `final`로 선언된 클래스는 **자식 클래스를 가질 수 없다 (상속 불가).**
-   클래스 설계가 완전하여 더 이상 확장될 필요가 없거나 보안이 중요할 때 사용한다. (예: `String`, `Integer` 클래스)

---

## 4. 추가적으로 알아야 할 중요 정보 (놓치기 쉬운 내용)

### 4.1. 참조 변수와 `final`: 불변의 오해

-   `final`이 참조 타입 변수(객체, 배열 등)에 사용될 때, **참조(주소값) 자체를 변경할 수 없다는 뜻이지, 그 참조가 가리키는 객체의 내용(상태)까지 변경할 수 없다는 의미는 아니다.**

```java
final List<String> list = new ArrayList<>();

// list = new LinkedList<>(); // 에러! 다른 객체를 가리키도록 참조를 바꿀 수 없다.

list.add("A"); // 정상 작동! list가 가리키는 객체의 내용은 변경 가능하다.
list.add("B");
list.remove(0);
```
-   객체의 내용까지 완전히 불변으로 만들고 싶다면 `List.of()` (Java 9+)와 같은 불변 컬렉션을 사용해야 한다.

### 4.2. `final`의 이점: 왜 사용하는가?

1.  **안정성 (Safety)**: 값이 변하지 않음을 보장하므로, 코드의 동작을 예측하기 쉬워지고 버그가 줄어든다. 특히 멀티스레드 환경에서 스레드 간 데이터 공유 시 안전성을 높인다.
2.  **가독성 (Readability)**: `final` 키워드를 보면 "이 값은 변하지 않는 중요한 값"이라는 것을 개발자가 즉시 인지할 수 있다.
3.  **보안 (Security)**: `final` 클래스나 메서드를 통해 핵심 로직이 임의로 변경되는 것을 막아 시스템을 보호할 수 있다.
