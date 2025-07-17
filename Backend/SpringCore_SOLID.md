
# SOLID 원칙에 대하여

프로그램을 필요에 따라 쉽게 바꾸고, 확장하고, 고칠 수 있도록 만들어주는 설계법.

기계의 모듈화공법처럼 부품마다 역할이 정해져있고 분리 및 교체가 쉬우면 유지보수가 쉽다 라는 개념

## 1. S: 단일 책임 원칙

하나의 클래스는 하나의 기능, 하나의 역할만 전문적으로 수행해야한다.

## 2. O: 개방-패쇄 원칙

새로운 기능을 추가할 때, 기존 코드를 고치는 것이 아닌 새로운 코드를 추가하여 만들어지도록 해야한다.

## 3. L: 리스코프 치환 원칙
부모클래스가 들어갈 자리에 자식 클래스를 대신 넣어도, 프로그램이 아무 문제 없이 똑같이 동작해야한다.

## 4. I: 인터페이스 분리 원칙
인터페이스 또한 기능별로 잘게 쪼개서 여러 개를 만드는 것이 더 좋다는 원칙

## 4. D: 의존성 역전 원칙
저수준 모듈에 직접 의존하지 말고, 공통적인 약속(추상화)에 의존하라는 원칙


``` java
// '입력 장치'라는 약속(인터페이스)을 만들어요.
public interface InputDevice { /* ... */ }

// 키보드는 그 약속을 지켜서 만들어요.
public class Keyboard implements InputDevice { /* ... */ }
// 마우스도 그 약속을 지켜서 만들어요.
public class Mouse implements InputDevice { /* ... */ }

// 컴퓨터는 이제 '입력 장치'면 뭐든지 환영!
public class Computer {
    private InputDevice inputDevice; // 특정 제품이 아닌 '규격'에 의존

    // 외부에서 '입력 장치'를 만들어서 넣어줌 (의존성 주입)
    public Computer(InputDevice inputDevice) {
        this.inputDevice = inputDevice;
    }
}

