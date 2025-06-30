

# 자바 접근 제어자(Access Modifier) 정리

자바에서 **접근 제어자**는 클래스, 필드(멤버 변수), 메서드, 그리고 생성자에 대한 접근 범위를 제어하는 키워드이다. 이를 통해 코드의 **캡슐화**를 구현하고, 클래스 간의 불필요한 의존성을 줄여 유지보수성을 높인다.

-----

### 1\. 접근 제어자의 종류와 범위

| 제어자    | 같은 클래스 내부 | 같은 패키지 내부 | 다른 패키지 (상속 관계 아님) | 다른 패키지 (상속 관계) |
| :-------- | :--------------- | :--------------- | :------------------- | :------------------- |
| `private` | O                | X                | X                    | X                    |
| `default` | O                | O                | X                    | X                    |
| `protected` | O                | O                | X                    | O                    |
| `public`  | O                | O                | O                    | O                    |

  * **`private`**: 가장 엄격한 접근 제어자이다. 선언된 클래스 내부에서만 접근할 수 있다. 주로 외부에서 직접 접근해서는 안 되는 중요한 데이터를 캡슐화할 때 사용한다.

  * **`default` (패키지-private)**: 아무런 접근 제어자도 명시하지 않으면 `default` 접근 제어를 가진다. 같은 패키지 내의 클래스에서만 접근할 수 있다.

  * **`protected`**: 같은 패키지 내의 클래스에서 접근할 수 있으며, 다른 패키지에 있더라도 해당 클래스를 상속받은 자식 클래스에서는 접근할 수 있다. 상속 관계에서 부모 클래스의 특정 멤버를 자식 클래스에서만 접근하게 할 때 유용하다.

  * **`public`**: 가장 넓은 접근 범위를 가진다. 어떤 클래스에서든 접근할 수 있다. 일반적으로 외부에 공개되어야 하는 API나 메서드에 사용된다.

-----

### 2\. 클래스에 붙일 수 있는 접근 제어자

**최상위(Top-level) 클래스**에는 `public` 또는 `default`만 붙일 수 있다.

  * **`public class MyClass {}`**: 어떤 패키지에서도 접근 가능한 클래스이다.
  * **`class MyClass {}`**: 같은 패키지 내에서만 접근 가능한 클래스이다.

\*\*내부 클래스(Inner Class)\*\*는 `private`, `default`, `protected`, `public` 모두 붙일 수 있다.

-----

### 3\. 필드, 메서드, 생성자에 대한 접근 제어자

위에서 설명한 `private`, `default`, `protected`, `public` 네 가지 접근 제어자 모두 필드, 메서드, 생성자에 적용할 수 있다.

**예시 코드 설명:**


```java
// chap1_6.modi.pac1 패키지
package chap1_6.modi.pac1;

public class A {
    public int f1;    // public 필드
    int f2;           // default 필드
    private int f3;   // private 필드

    public void m1(){}    // public 메서드
    void m2(){}           // default 메서드
    private void m3(){}   // private 메서드

    public A(){      // public 생성자
       this(1.5);    // (private 생성자 호출 가능 - 같은 클래스 내부이므로)
       // f1, f2, f3, m1(), m2(), m3() 모두 접근 가능 (같은 클래스 내부이므로)
    }
    A(int a){}       // default 생성자
    private A(double a){} // private 생성자
}
```

  * **`A` 클래스 내부 (`public A()` 생성자)**: 같은 클래스 내부에서는 `public`, `default`, `private` 등 모든 접근 제어자와 상관없이 모든 필드, 메서드, 생성자에 **자유롭게 접근 가능**하다. `this(1.5)`와 같이 `private` 생성자를 호출하는 것도 가능하다.

<!-- end list -->

```java
// chap1_6.modi.pac1 패키지 (A와 같은 패키지)
package chap1_6.modi.pac1;

class B {
    public B(){
        A a = new A();           // A의 public 생성자 호출 가능
        A b = new A(100);        // A의 default 생성자 호출 가능
        // A c = new A(1.5);    // A의 private 생성자 호출 불가능

        a.f1 =10;                // A의 public 필드 f1 접근 가능
        a.f2 =20;                // A의 default 필드 f2 접근 가능
        // a.f3 =30;            // A의 private 필드 f3 접근 불가능

        a.m1();                  // A의 public 메서드 m1 접근 가능
        a.m2();                  // A의 default 메서드 m2 접근 가능
        // a.m3();              // A의 private 메서드 m3 접근 불가능
    }
}
```

  * **`B` 클래스 내부 (`A`와 같은 패키지)**: `B` 클래스는 `A` 클래스와 같은 `chap1_6.modi.pac1` 패키지에 있다. 따라서 `A`의 `public` 및 `default` 멤버(필드, 메서드, 생성자)에 접근할 수 있지만, `private` 멤버에는 접근할 수 없다. 클래스 `B` 자체는 `default` 접근 제어를 가지므로 다른 패키지에서는 접근할 수 없다.

<!-- end list -->

```java
// chap1_6.modi.pac2 패키지 (A와 다른 패키지)
package chap1_6.modi.pac2;

import chap1_6.modi.pac1.A; // A 클래스를 사용하려면 import해야 한다.

public class C {
    C(){
        A a = new A();           // A의 public 생성자 호출 가능
        // A b = new A(200);    // A의 default 생성자 호출 불가능
        // A c = new A(2.1);    // A의 private 생성자 호출 불가능

        a.f1 =10;                // A의 public 필드 f1 접근 가능
        // a.f2 =20;            // A의 default 필드 f2 접근 불가능
        // a.f3 =30;            // A의 private 필드 f3 접근 불가능

        a.m1();                  // A의 public 메서드 m1 접근 가능
        // a.m2();              // A의 default 메서드 m2 접근 불가능
        // a.m3();              // A의 private 메서드 m3 접근 불가능

        // new B();             // B 클래스가 default라 다른 패키지에서는 접근 불가능
    }
}
```

  * **`C` 클래스 내부 (`A`와 다른 패키지)**: `C` 클래스는 `A` 클래스와 다른 `chap1_6.modi.pac2` 패키지에 있다. 다른 패키지에 있는 클래스의 멤버에 접근하려면 `import` 문을 사용해야 한다. 다른 패키지에서는 **`public` 멤버에만 접근**할 수 있다. `default`나 `private` 멤버에는 접근할 수 없다. 또한 `B` 클래스가 `default` 접근 제어를 가지고 있으므로, `B` 클래스 자체도 다른 패키지에서 인스턴스화하거나 접근할 수 없다.

-----

### 결론

접근 제어자는 객체 지향 프로그래밍의 중요한 원칙인 **캡슐화**를 실현하는 핵심적인 요소이다. 이를 통해 코드의 응집도를 높이고 결합도를 낮춰 더 견고하고 유연한 소프트웨어를 개발한다. 각 상황에 맞는 적절한 접근 제어자를 사용하는 것이 중요하다.