

## 자바 캡슐화(Encapsulation)와 접근 제어자 활용

 캡슐화는 \*\*데이터(필드)\*\*와 \*\*데이터를 다루는 기능(메서드)\*\*을 하나의 단위(클래스)로 묶고, 외부에서 객체 내부의 상세 구현을 직접 보거나 변경하지 못하도록 **정보를 은닉**하는 것을 의미한다. 이를 통해 데이터 무결성을 보장하고 유지보수성을 높인다.

-----

### `Car` 클래스 분석: 정보 은닉의 구현

`Car` 클래스는 자동차 객체의 속성(필드)과 동작(메서드)을 정의한다. 여기서 **접근 제어자**(`private`, `public`)를 사용하여 캡슐화를 구현한다.

  * **`private` 필드**:

    ```java
    private String model;      // 모델명
    private String mode;       // 변속모드(P,R,N,D)
    private String ownerName;  // 차주명
    private boolean on;        // 시동여부
    ```

    `model`, `mode`, `ownerName`, `on`과 같은 필드들은 모두 `private`으로 선언되어 있다. 이는 **클래스 외부(예: `Main` 클래스)에서 이 필드들에 직접 접근하거나 값을 변경할 수 없도록** 정보를 은닉한다. 만약 이 필드들이 `public`이었다면, `Main` 클래스에서 `myCar.mode = "X";` 와 같이 잘못된 값을 직접 할당할 수 있어 데이터 무결성이 깨질 수 있다.

  * **`private` 메서드**:

    ```java
    private void injectOil(){ ... }     // 엔진오일 분사
    private void moveCylinder(){ ... }  // 엔진 실린더 작동
    private void powerOn(){ ... }       // 시동 걸기
    ```

    `injectOil()`, `moveCylinder()`, `powerOn()` 메서드들 또한 `private`으로 선언되어 있다. 이 메서드들은 자동차 시동 메커니즘의 **내부적인 구현 상세**를 나타낸다. 외부에서 이 기능들을 직접 호출하게 할 필요가 없으며, 잘못된 순서로 호출될 경우 문제가 발생할 수 있다. 따라서 이들은 `pressButton()`과 같은 **공개된(public) 메서드를 통해서만 간접적으로 호출**되도록 한다.

  * **`public` 메서드 (게터/세터 및 핵심 기능)**:

    ```java
    // 변속을 수행하는 메서드 (setter)
    public void setMode(String mode){
        switch(mode){
            case "D": case "N": case "P": case "R":
                this.mode = mode;
                break;
            default:
                this.mode = "P"; // 잘못된 입력 시 기본값으로 설정
        }
    }

    // 현재 변속 모드를 반환하는 메서드 (getter)
    public String getMode(){
        return mode;
    }

    // 시동 버튼을 누르는 기능 (핵심 공개 기능)
    public void pressButton(){
        injectOil();
        powerOn();
        moveCylinder();
        System.out.println("시동 버튼을 눌렀다.");
    }
    ```

    `setMode()`와 `getMode()`는 각각 특정 필드(`mode`)의 값을 \*\*안전하게 설정(setter)\*\*하고 \*\*조회(getter)\*\*하기 위해 제공되는 `public` 메서드이다. `setMode()` 메서드 내부에서 입력값(`mode`)의 유효성을 검사하여 올바른 값만 설정되도록 제어한다. `getMode()`는 현재 `mode`의 값을 외부로 노출한다.

    `pressButton()` 메서드 역시 `public`이다. 이 메서드는 자동차의 \*\*"시동 버튼 누르기"\*\*라는 외부에서 직접 사용할 수 있는 기능을 나타낸다. 이 메서드 내부에서 `private` 메서드들(`injectOil()`, `powerOn()`, `moveCylinder()`)을 적절한 순서로 호출하여 시동 과정을 처리한다. 외부에서는 단순히 `pressButton()`만 호출하면 되고, 내부적인 복잡한 시동 과정은 알 필요가 없다.

-----

### `Main` 클래스 분석: 캡슐화의 효과

`Main` 클래스는 `Car` 클래스를 사용하는 예시를 보여준다.

```java
package chap1_6.encap;

public class Main {
    public static void main(String[] args) {
        Car myCar = new Car();

        // 캡슐화를 통해 안전하게 기능 사용
        myCar.pressButton(); // 시동 관련 복잡한 내부 동작을 몰라도 됨

        // 기어 변속 시 setter 메서드를 통해 유효성 검사 적용
        myCar.setMode("D");
        System.out.println("현재 모드: " + myCar.getMode());

        // myCar.mode = "X"; // private 필드이므로 직접 접근 불가능 (컴파일 에러)
        // myCar.injectOil(); // private 메서드이므로 직접 호출 불가능 (컴파일 에러)
    }
}
```

  * `myCar.pressButton();`를 호출한다. `Main` 클래스는 `Car` 객체의 내부 시동 과정(엔진오일 분사, 시동 걸기, 실린더 작동)에 대해 전혀 알 필요가 없다. 그저 "시동 버튼을 누른다"는 추상적인 개념으로 기능을 사용한다. 이는 **높은 응집도와 낮은 결합도**를 달성하는 예시이다.
  * `myCar.setMode("D");`를 통해 `mode` 필드의 값을 변경한다. `setMode()` 메서드 내부에서 "D"가 유효한 값인지 검사하므로, `Main`에서 잘못된 값을 전달하더라도 `Car` 객체의 상태가 비정상적으로 되는 것을 방지한다.
  * `myCar.getMode()`를 통해 현재 `mode` 값을 안전하게 조회한다.

-----

### 결론

이 코드는 **캡슐화**를 통해 객체 내부의 중요한 데이터를 \*\*`private`\*\*으로 보호하고, 해당 데이터에 대한 접근 및 조작을 **`public` 메서드**를 통해서만 허용함으로써 데이터의 **무결성**을 보장하는 방법을 명확하게 보여준다. 또한, 복잡한 내부 구현 상세를 외부에 노출하지 않고, 사용자에게는 필요한 기능(인터페이스)만을 제공하여 코드의 **재사용성, 유지보수성, 확장성**을 크게 향상시킨다.