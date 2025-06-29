



# 패키지와 import

## 1. 패키지(Package)

### 1.1 패키지의 개념
- 자바에서 패키지는 클래스 파일들을 논리적으로 묶어주는 단위이다.
- 실제로는 클래스 파일들을 디렉터리 구조로 구분하여 관리한다.
- `package` 키워드를 사용하여 클래스 상단에 명시한다.

### 1.2 패키지 이름 작성 규칙
1. 패키지 이름은 **무조건 소문자**로 작성하는 것이 관례이다.
2. 변수, 메서드와 동일한 규칙이 적용된다.
   - 공백 사용 불가
   - 숫자로 시작 불가
   - 예약어 사용 불가
   - 특수문자 사용 불가
3. 띄어쓰기를 표현할 경우, 점(`.`)을 사용하여 구분한다.
   - 예: `my phone number` → `my.phone.number`
4. `java`로 시작하는 패키지 이름은 사용하지 않는다.
   - 이유: 자바의 기본 라이브러리와 혼동될 수 있기 때문이다.

---

## 2. import 문

### 2.1 import의 개념
- import는 다른 패키지에 정의된 클래스를 현재 클래스에서 사용할 수 있도록 해주는 선언문이다.
- 같은 패키지 내의 클래스는 import 없이 사용할 수 있으나, 다른 패키지의 클래스는 반드시 import가 필요하다.

### 2.2 import 사용 방법
- 특정 클래스만 import하는 방법:
  ```java
  import chap1_4.cake.Banana;
  import chap1_4.cake.Grape;





* 패키지 내 모든 클래스를 import하는 방법:

  ```java
  import chap1_4.cake.*;
  ```

  `*`를 사용하면 해당 패키지 내의 모든 클래스를 한 번에 import할 수 있다.

### 2.3 주의사항

* 서로 다른 패키지에 동일한 이름의 클래스가 존재할 경우, 해당 클래스명을 사용할 때에는 **패키지 경로를 명시**해야 한다.

  ```java
  chap1_4.juice.Apple juiceApple = new chap1_4.juice.Apple();
  chap1_4.cake.Apple cakeApple = new chap1_4.cake.Apple();
  ```

---

## 3. 예제 코드

```java
import chap1_4.cake.*; // Banana, Grape 클래스 포함

public class Main {
    public static void main(String[] args) {
        chap1_4.juice.Apple juiceApple = new chap1_4.juice.Apple();
        chap1_4.cake.Apple cakeApple = new chap1_4.cake.Apple();

        Banana banana = new Banana(); // import된 클래스 사용
        Grape grape = new Grape();
    }
}
```

---

## 4. 요약

* 패키지는 자바 클래스의 논리적 분류 단위이며, 소문자로 구성한다.
* import는 다른 패키지의 클래스를 사용할 때 필요하며, `*`로 전체 클래스를 가져올 수 있다.
* 동일한 클래스명이 서로 다른 패키지에 존재할 경우, 전체 경로를 명시하여 사용해야 한다.

