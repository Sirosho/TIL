# 📦 Java 파일 입출력 및 객체 직렬화 정리

> 파일 전체에서 공통적으로 다루는 주제는 크게 **파일 입출력(IO)** 과 **객체 직렬화/역직렬화**임.

---

## 📁 1. 파일 입출력의 기본

Java는 파일에서 데이터를 읽거나 쓸 때 **입출력 스트림**을 사용한다.  
입출력 방식은 크게 **바이트 기반**과 **문자 기반**으로 나뉜다.

### ✔ 바이트 기반 스트림

- `FileInputStream`, `FileOutputStream`  
- 데이터를 **바이트(byte)** 단위로 읽거나 쓴다.  
- 예: 이미지, 영상, 바이너리 파일 등 처리에 적합

```java
FileInputStream fis = new FileInputStream("input.txt");
int data = fis.read(); // 1바이트씩 읽음
```

### ✔ 문자 기반 스트림

- `FileReader`, `FileWriter`  
- 데이터를 **문자(char)** 단위로 읽거나 쓴다.  
- 예: 텍스트 파일 처리에 적합

```java
FileReader fr = new FileReader("file.txt");
int data = fr.read(); // 1글자씩 읽음
```

### ✔ 예외 처리와 리소스 해제

- `try-with-resources` 구문을 활용하여 자동으로 스트림을 닫는다.  
- 예외 발생 시에도 안전하게 리소스가 해제된다.

---

## 📄 2. File 클래스 활용

`java.io.File` 클래스는 **파일 자체를 다루는 기능**을 제공한다.  
주요 기능은 다음과 같다.

| 기능             | 메서드                          |
|------------------|----------------------------------|
| 존재 여부 확인   | `exists()`                       |
| 파일 생성        | `createNewFile()`                |
| 디렉토리 생성    | `mkdir()`                        |
| 파일 정보 확인   | `getName()`, `getAbsolutePath()`, `length()` |

---

## 🧊 3. 객체 직렬화 (Serialization)

> 자바 객체를 파일에 저장하거나 네트워크로 전송하기 위해 **이진 형태로 변환**하는 과정이다.

### ✔ 직렬화할 클래스 조건

- `Serializable` 인터페이스를 **반드시 구현**해야 한다.  
- 객체 내부에 존재하는 모든 필드도 직렬화 가능해야 한다.

```java
public class Snack implements Serializable {
    private String name;
    private int price;
    private double weight;
}
```

### ✔ 직렬화와 저장

- `ObjectOutputStream` 을 사용하여 객체를 저장한다.  
- `writeObject()` 메서드로 객체를 출력한다.

```java
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("snacks.dat"));
oos.writeObject(snackArray);
```

### ✔ 역직렬화와 불러오기

- `ObjectInputStream` 을 통해 객체를 읽어온다.  
- `readObject()` 메서드를 통해 파일에서 객체를 복원한다.

```java
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("snacks.dat"));
Snack[] snacks = (Snack[]) ois.readObject();
```

---

## ⚠️ 주의 사항

- 클래스 구조가 변경되면 역직렬화가 실패할 수 있음 (`InvalidClassException`)  
- 파일이 존재하지 않으면 `FileNotFoundException` 발생  
- 스트림 사용 후 반드시 `close()` 하거나 `try-with-resources`로 처리해야 함

---

## ✅ 전반적인 흐름 요약

1. 파일에 텍스트나 객체를 저장하려면 **스트림**을 열고 write 작업을 수행한다.  
2. 텍스트 저장은 `FileWriter`, 객체 저장은 `ObjectOutputStream`을 사용한다.  
3. 저장된 파일을 읽을 때는 `FileReader` 또는 `ObjectInputStream`을 사용한다.  
4. 객체를 저장하려면 반드시 `Serializable`을 구현해야 하며, 역직렬화 시 캐스팅 주의가 필요하다.  
5. 파일과 관련된 정보 조회, 생성, 삭제는 `File` 클래스를 통해 수행한다.
