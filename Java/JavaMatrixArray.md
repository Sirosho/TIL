

# Matrix

## 🔠 2차원 배열 (다차원 배열): 행과 열로 데이터 관리하기

**2차원 배열**은 이름 그대로 배열 안에 또 다른 배열이 들어있는 구조다. 마치 스프레드시트나 표처럼 **행(row)과 열(column)** 형태로 데이터를 저장하고 관리할 때 유용하게 사용된다.

개념적으로는 다음과 같은 형태를 생각할 수 있다:

```
[
  [1, 2, 3],  // 첫 번째 행
  [4, 5, 6],  // 두 번째 행
  [7, 8, 9]   // 세 번째 행
]
```

### 📝 2차원 배열의 선언과 접근

#### 1\. 2차원 배열 선언 및 초기화

2차원 배열은 중괄호를 중첩해서 선언하고 초기화할 수 있다.

```java
import java.util.Arrays; // 배열 내용을 출력하기 위해 필요

public class Matrix {

    public static void main(String[] args) {

        // 2차원 배열의 선언과 동시에 초기화
        int[][] matrix = {
            {10, 20, 30}, // 0번 행
            {40, 50, 60}, // 1번 행
            {70, 80, 90}  // 2번 행
        };

        // 2차원 배열 변수를 직접 출력하면 메모리 주소가 출력된다.
        // 각 행(내부 배열)의 주소도 별도로 출력할 수 있다.
        System.out.println("2차원 배열의 값: " + Arrays.toString(matrix));      // 출력: [[I@... , [I@... , [I@...] (내부 배열들의 주소)
        System.out.println("0번 인덱스(첫 번째 행)의 주소: " + matrix[0]); // 출력: [I@... (첫 번째 내부 배열의 주소)
        System.out.println("1번 인덱스(두 번째 행)의 주소: " + matrix[1]); // 출력: [I@...
        System.out.println("2번 인덱스(세 번째 행)의 주소: " + matrix[2]); // 출력: [I@...

        System.out.println("==========================");

        // 특정 요소에 접근하기: matrix[행 인덱스][열 인덱스]
        System.out.println(matrix[0][2]); // 출력: 30 (0번 행의 2번 인덱스 값)

        // 2차원 배열의 전체 내용을 확인하려면 Arrays.deepToString()을 사용한다.
        // Arrays.toString()은 내부 배열들의 주소만 보여주므로, 실제 값을 보려면 이 메서드를 써야 한다.
        System.out.println(Arrays.deepToString(matrix)); // 출력: [[10, 20, 30], [40, 50, 60], [70, 80, 90]]
    }
}
```

#### 2\. 빈 2차원 배열 생성

크기만 지정하고 값을 할당하지 않은 빈 2차원 배열도 생성할 수 있다. 이때 모든 요소는 해당 타입의 **기본값**으로 자동 초기화된다.

  * `int[][] arr2d = new int[10][10];` // 10행 10열 크기의 2차원 정수 배열을 생성한다. 모든 값은 `0`으로 초기화된다.

### 🔁 2차원 배열 순회 (반복문으로 모든 요소 접근)

2차원 배열의 모든 요소에 접근하려면, 일반적으로 중첩된 반복문(nested loop)을 사용한다.

```java
import java.util.Arrays;

public class Matrix {
    public static void main(String[] args) {
        int[][] matrix = {
            {10, 20, 30},
            {40, 50, 60},
            {70, 80, 90}
        };

        System.out.println("\n--- 2차원 배열 순회 ---");
        // 외부 반복문: 각 '행'에 해당하는 내부 배열을 하나씩 가져온다.
        for (int[] arr : matrix) {
            // 내부 반복문: 가져온 '행' 배열의 각 요소를 하나씩 출력한다.
            for (int i : arr) {
                System.out.print(i + " ");
            }
            System.out.println(); // 한 행의 출력이 끝나면 줄바꿈한다.
        }
        // 출력:
        // 10 20 30
        // 40 50 60
        // 70 80 90

        System.out.println("\n--- 빈 2차원 배열 순회 (기본값 확인) ---");
        int[][] arr2d = new int[3][4]; // 3행 4열 배열 생성 (모두 0으로 초기화)
        for (int[] arr : arr2d) {
            for (int i : arr) {
                System.out.print(i + " ");
            }
            System.out.println();
        }
        // 출력:
        // 0 0 0 0
        // 0 0 0 0
        // 0 0 0 0
    }
}
```

2차원 배열은 이미지 처리, 게임 보드, 그래프 등 행과 열의 구조를 갖는 데이터를 표현하는 데 매우 유용하게 사용된다.


### 2차원 배열의 전체 내용을 확인하려면 Arrays.deepToString()을 사용한다.
 `Arrays.toString()` 은 내부 배열들의 주소만 보여주므로, 실제 값을 보려면 이 메서드를 써야 한다.