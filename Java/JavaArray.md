

## 📦 배열 (Array): 데이터를 효율적으로 관리하는 법

자바에서 변수들은 보통 \*\*스택(Stack)\*\*이라는 메모리 영역에 저장된다. 스택은 빠르게 데이터를 찾을 수 있지만, 용량이 제한적이다. 그래서 많은 데이터를 효율적으로 저장하기 위해 \*\*배열(Array)\*\*을 사용한다. 배열은 \*\*힙(Heap)\*\*이라는 더 넓은 영역에 실제 데이터를 저장하고, 스택에는 해당 배열의 힙 메모리 주소만 저장한다.

### 📝 배열의 주요 특징

  * **동종 모음 구조:** 같은 타입의 데이터만 저장할 수 있다. (예: `int` 배열에는 `int`만, `String` 배열에는 `String`만 저장 가능)
  * **고정된 크기:** 배열은 일단 생성되면 크기(`length`)를 변경할 수 없다. 초기화하지 않은 공간은 해당 타입의 **기본값**으로 자동 초기화된다.

### 🛠️ 배열 선언, 생성, 초기화

배열을 사용하는 단계는 다음과 같다.

#### 1\. 배열 변수 선언 (스택에 변수 선언)

배열의 주소를 저장할 변수를 스택에 선언한다. 이 변수 자체는 아직 데이터를 담는 공간이 아니다.

  * `int[] numbers;` // 타입 뒤에 `[]`를 붙여 배열 변수임을 나타낸다.

#### 2\. 배열 생성 (힙에 데이터 저장 공간 할당)

`new` 키워드를 사용하여 힙 메모리에 실제 데이터를 저장할 공간을 할당한다. 이때 배열의 **크기**를 명시한다.

  * `numbers = new int[5];` // `int` 타입의 요소를 5개 저장할 수 있는 공간을 힙에 만든다.

#### 3\. 배열 초기화 (값 저장)

생성된 배열 공간에 실제 값을 저장한다. 배열의 각 요소는 \*\*인덱스(index)\*\*를 사용하여 접근한다. 인덱스는 `0`부터 시작한다.

  * `numbers[0] = 50;`
  * `numbers[1] = 77;`
  * `numbers[2] = (int) 66.7;` // 타입에 맞춰 명시적 형 변환을 할 수 있다.
  * `numbers[3] = numbers[0] * 3;`
  * `numbers[4] = 99;`

#### 4\. 배열 내용 확인

배열 변수를 직접 출력하면 메모리 주소가 출력된다. 배열의 실제 내용을 보려면 `Arrays.toString()` 메서드를 사용해야 한다.

  * `System.out.println(numbers);` // 예: `[I@4c873330` (메모리 주소)
  * `System.out.println(Arrays.toString(numbers));` // 예: `[50, 77, 66, 150, 99]` (배열 데이터)

-----

### 📏 배열의 길이와 순회

#### 배열 길이 확인

배열의 크기(`length`)는 `배열이름.length`로 확인할 수 있다. 이는 자바스크립트와 유사하다.

  * `System.out.println(numbers.length);` // 예: `5`

#### 배열 순회 (Iteration)

배열의 모든 요소에 접근하여 처리할 때 반복문을 사용한다.

  * **기본 `for` 루프:**

    ```java
    for (int i = 0; i < numbers.length; i++) {
        System.out.println("데이터 = " + numbers[i]);
    }
    ```

  * **향상된 `for` 루프 (Enhanced for loop / for-each loop):**
    자바스크립트의 `for...of` 문과 유사하다. 배열의 모든 요소를 순서대로 하나씩 가져올 때 편리하다.

    ```java
    for (int number : numbers) { // numbers 배열의 각 요소를 number 변수에 담아 반복한다.
        System.out.println(number);
    }
    ```

-----

### ➡️ 배열 단축 생성 문법

배열을 선언과 동시에 초기화할 때 사용할 수 있는 간편한 문법이다.

  * **일반적인 생성 및 초기화:**
    `String[] pets = new String[] {"멍멍이", "야옹이", "짹짹이"};`

  * **선언 시에만 `new Type[]` 생략 가능:**
    배열 변수를 **선언하는 동시에 초기화**할 때만 `new String[]` 부분을 생략할 수 있다.

    `String[] pets = {"멍멍이", "야옹이", "짹짹이"};`

    하지만 이미 선언된 배열 변수에 값을 재할당할 때는 `new Type[]`를 생략할 수 없다.

    `pets = new String[]{"어흥이", "톰톰이"};` // 올바른 재할당
    `pets = {"어흥이", "톰톰이"};` // (X) 이렇게 사용하면 컴파일 에러 발생

-----

### 🈳 배열의 기본값

자바 배열은 생성되는 순간, 할당된 모든 공간에 해당 데이터 타입의 **기본값**으로 자동 초기화된다. 이는 메모리 공간이 비어 있지 않도록 보장한다.

  * **정수형 (byte, short, int, long):** `0`

      * `byte[] bArr = new byte[3];` // `[0, 0, 0]`
      * `System.out.println(Arrays.toString(bArr));`

  * **실수형 (float, double):** `0.0`

      * `double[] dArr = new double[3];` // `[0.0, 0.0, 0.0]`
      * `System.out.println(Arrays.toString(dArr));`

  * **논리형 (boolean):** `false`

      * `boolean[] blArr = new boolean[3];` // `[false, false, false]`
      * `System.out.println(Arrays.toString(blArr));`

  * **문자형 (char):** `'\u0000'` (null 문자, ASCII 0)

      * `char[] cArr = new char[3];` // `['\u0000', '\u0000', '\u0000']`
      * `System.out.println(Arrays.toString(cArr));`

  * **참조 타입 (Reference Types) (String 등):** `null`

      * `String[] sArr = new String[3];` // `[null, null, null]`
      * `System.out.println(Arrays.toString(sArr));`

**참고:** 자바의 타입은 크게 \*\*기본 타입(primitive types)\*\*과 \*\*참조 타입(reference types)\*\*으로 나뉜다.

  * **기본 타입:** `byte`, `short`, `int`, `long`, `float`, `double`, `boolean`, `char` (실제 값을 저장)
  * **참조 타입:** `String`, 배열, 클래스, 인터페이스 등 (객체의 주소를 저장하며, 변수명이 보통 대문자로 시작하는 파스칼 케이스를 따른다.)

-----