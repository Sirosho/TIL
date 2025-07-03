
# 자바 열거형(Enum) 타입 활용

## 1. 개요

 열거형을 사용함으로써 코드의 **타입 안전성(Type Safety)**과 **가독성(Readability)**을 어떻게 향상시킬 수 있는지 공부하였다

---

## 2. 열거형(Enum)의 정의와 특징

### 2.1. 기본 개념

-   열거형은 서로 연관된 **상수(Constant)들의 집합**을 정의하는 특별한 데이터 타입이다.
-   `ORDERED`, `READY`, `DELIVERED`와 같이 정해진 값들만 가질 수 있도록 강제하여, 문자열이나 정수 상수를 사용할 때 발생할 수 있는 오류를 원천적으로 방지한다.
-   예시: `status = "주문 완료"` (오타 발생 가능) vs `status = ORDERED` (컴파일 시점에 체크 가능)

### 2.2. 멤버 추가: 필드, 생성자, 메서드

-   Java의 `enum`은 단순한 상수 집합을 넘어, 클래스처럼 **필드, 생성자, 메서드**를 가질 수 있다.
-   이를 통해 상수 값에 추가적인 데이터와 기능을 부여할 수 있다.

#### 코드 예시: `PizzaStatus.java`
```java
package chap1_9.enum_;

public enum PizzaStatus {
    // 각 상수는 생성자를 통해 고유한 데이터를 가진다.
    ORDERED("주문 완료", 10),
    READY("준비 완료", 30),
    DELIVERED("배달 완료", 40)
    ; // 멤버를 추가할 경우, 상수 목록 끝에 세미콜론(;)을 붙여야 한다.

    // 1. 필드 선언
    private final String description; // 상태 설명
    private final int timeToSetup;    // 준비 시간

    // 2. 생성자 선언 (enum의 생성자는 항상 private 이다)
    PizzaStatus(String description, int timeToSetup) {
        this.description = description;
        this.timeToSetup = timeToSetup;
    }

    // 3. 메서드 선언
    public String getDescription() {
        return description;
    }

    public int getTimeToSetup() {
        return timeToSetup;
    }
}
```

-   **생성자**: `enum`의 생성자는 외부에서 호출할 수 없으며(항상 `private`), `enum`이 로드될 때 각 상수별로 한 번씩만 호출된다.
-   **필드**: 각 상수가 고유한 상태(값)를 저장하도록 할 수 있다. `description`과 `timeToSetup`이 그 예이다.

### 2.3. 정적 임포트 (Static Import)

-   `import static` 구문을 사용하면 `enum`의 이름을 생략하고 상수 이름을 바로 사용할 수 있어 코드가 간결해진다.

#### 코드 예시: `Pizza.java` 상단
```java
// PizzaStatus.ORDERED 대신 ORDERED 라고 바로 사용할 수 있게 해준다.
import static chap1_9.enum_.PizzaStatus.*;

public class Pizza {
    // ...
}
```

---

## 3. 코드에서의 활용 사례

### 3.1. 상태 관리

-   `Pizza` 클래스는 `PizzaStatus` 타입의 `status` 필드를 통해 피자의 현재 상태를 관리한다.
-   상태를 변경하거나 확인할 때, `enum` 상수를 직접 비교함으로써 명확하고 안전한 코드를 작성할 수 있다.

#### 코드 예시: `Pizza.java`
```java
public class Pizza {
    private PizzaStatus status; // 피자 상태를 Enum 타입으로 관리

    // 피자 주문 기능
    public void orderPizza() {
        this.status = ORDERED; // 정적 임포트로 PizzaStatus. 생략
        System.out.println("피자 주문이 들어왔습니다.");
    }
    
    // 피자 요리 완료 기능
    public void readyPizza() {
        // == 연산자로 타입-안전하게 상태를 비교
        if (this.status == ORDERED) { 
            this.status = READY;
            System.out.println("피자 요리가 완료되었습니다.");
        } else {
            System.out.println("아직 피자 주문이 없습니다.");
        }
    }
    // ...
}
```

### 3.2. 데이터와 행위의 결합

-   `Pizza` 클래스는 상태에 대한 설명을 얻기 위해 `status` 변수가 가진 `getDescription()` 메서드를 호출한다.
-   이는 상태(데이터)와 그 상태에 대한 설명(기능)이 `PizzaStatus` `enum` 내에 함께 캡슐화되어 있기에 가능하다.

#### 코드 예시: `Pizza.java`
```java
public String getStatus() {
    // status 변수 자체가 getDescription() 메서드를 가지고 있음
    return status.getDescription(); 
}
```

---

## 4. 열거형 사용의 핵심 이점

1.  **타입 안전성 (Type Safety)**
    -   정의된 상수 외의 다른 값이 변수에 할당되는 것을 컴파일러가 막아준다.

2.  **가독성 (Readability)**
    -   `status == 1` 보다 `status == ORDERED` 가 훨씬 직관적이고 이해하기 쉽다.

3.  **데이터와 기능의 통합**
    -   상수와 관련된 데이터, 그리고 그 데이터를 처리하는 로직을 `enum` 안에 함께 묶어 관리할 수 있다.

4.  **유지보수성**
    -   새로운 상태가 추가되거나 기존 상태의 속성을 변경할 때, `enum` 파일만 수정하면 되므로 관리가 용이하다.
