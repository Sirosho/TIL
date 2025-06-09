오늘은 JS 고차함수까지 배우면서 전체적으로 부족했던 부분을 복습하면서 따로 정리해둬야겠다.


# 객체

### 객체 프로퍼티 참조법

### 1. 점 표기법 `user.name` 식

```js
const user = {
  name: '승현',
  age: 30
};

console.log(user.name); // '승현'

```

### 2. 대괄호 표기법 (변수활용)

```js
const key = 'name';
console.log(user[key]); // '승현'

```

[] 안의 내용을 변수로 판단하려 하기때문에 

user[name] 식으로 직접 프로퍼티를 적으면 참조가 되지 않는다.



### 객체 조작 기능

| 기능           | 문법 / 메서드                                          | 설명                          |
| ------------ | ------------------------------------------------- | --------------------------- |
| ✅ 존재 여부 확인   | `"key" in obj`                                    | 객체에 키가 있는지 확인               |
| ✅ 삭제         | `delete obj.key`                                  | 객체에서 특정 프로퍼티 제거             |
| ✅ 키 목록       | `Object.keys(obj)`                                | 객체의 모든 키 배열로 반환             |
| ✅ 값 목록       | `Object.values(obj)`                              | 객체의 모든 값 배열로 반환             |
| ✅ \[키,값] 목록  | `Object.entries(obj)`                             | \[키, 값] 쌍의 배열 반환            |
| ✅ 복사 (얕은 복사) | `Object.assign({}, obj)`<br>`{...obj}`            | 객체 복사 및 병합                  |
| ✅ 병합         | `Object.assign(target, source)`<br>`{...a, ...b}` | 여러 객체를 하나로 합치기              |
| ✅ 프로퍼티 정의    | `Object.defineProperty()`                         | 프로퍼티의 세부 속성 직접 제어 (예: 읽기전용) |
| ✅ 동결         | `Object.freeze(obj)`                              | 객체를 완전히 불변으로 만듦             |
| ✅ 확장 금지      | `Object.preventExtensions(obj)`                   | 더 이상 프로퍼티 추가 금지             |
| ✅ sealed     | `Object.seal(obj)`                                | 키 추가/삭제는 불가능, 값 수정은 가능      |



<div style="margin-top:100px;"></div>



### 참고✅ 모든 구조분해할당은 원본의 내용을 손상시키지 않는다.


<div style="margin-top:50px;"></div>

# 📌 1. 배열 구조 분해 할당 기본 문법
```js
const userNames = ['김철수', '홍길동', '고구마'];

// 기존 방식
const kim = userNames[0];
const hong = userNames[1];

// ES6 방식
const [kim, hong, go] = userNames;
console.log(kim, hong, go); // 김철수 홍길동 고구마
```

<div style="margin-top:100px;"></div>

# ✂️ 2. 배열 일부만 꺼내고 싶은 경우
```js
const [kk, , gg] = userNames;
console.log(kk, gg); // 김철수 고구마
```

### ✅ 넘기고싶은 인덱스는 빈칸으로 생략 가능. 자리는 유지하면서 원하는 인덱스만 변수화할 수 있음.

<div style="margin-top:100px;"></div>

# 🧪 3. 배열 + 함수가 같이 있을 때
```js
const example = [[10, 20], () => console.log('hello')];

const [nums, greet] = example;
console.log(nums); // [10, 20]
greet();           // hello
```
✅ 구조분해할당으로 배열/함수 섞인 구조도 쉽게 분리 가능!

<div style="margin-top:100px;"></div>

# 🔄 4. 값 스왑(swap)
```js
let first = 10, seconds = 20;
[first, seconds] = [seconds, first];
console.log(`first${first}, seconds${seconds}`); // first20, seconds10
```
### ✅ 구조분해를 이용하면 추가 변수 없이 값 교환 가능

<div style="margin-top:100px;"></div>

# 📦 5. 나머지 요소 묶기: Rest 문법 (...)
```js
const nums = [1, 3, 5, 7, 9, 11];
const [one, three, ...others] = nums;

console.log(one);     // 1
console.log(three);   // 3
console.log(others);  // [5, 7, 9, 11]
```

### ✅ 앞부분은 개별 변수로, 나머지는 ...변수명 으로 배열로 묶음

<div style="margin-top:100px;"></div>

# 🧨 6. 스프레드(spread) 문법
## ✨ 배열 결합

```js
const foods = ['햄버거', '콜라', '감자튀김'];
const fruits = ['오렌지', '레몬', '라임'];

const combination = [...foods, ...fruits];
console.log(combination);
// [ '햄버거', '콜라', '감자튀김', '오렌지', '레몬', '라임' ]
```
### ✅ ...배열명은 배열을 낱개 요소로 펼쳐서 추가함

## 🔁 배열 복사
```js
const newFruits = [...fruits];
```
### ✅ slice()나 Array.from() 대신 ...로 얕은 복사 가능

## ➕ 배열 확장 (새 항목 추가)
```js
const newFoods = [...foods, '새 데이터'];
console.log(newFoods); 
// ['햄버거', '콜라', '감자튀김', '새 데이터']
```
### ✅ push()는 원본 배열을 수정하지만, 스프레드는 원본 유지

<div style="margin-top:100px;"></div>

# ✅ 총정리 요약표

| 문법                   | 설명          | 예시                         |
| -------------------- | ----------- | -------------------------- |
| `[a, b] = arr`       | 구조 분해 할당    | `[a, b] = [1, 2]`          |
| `[a, , c]`           | 특정 인덱스 건너뛰기 | `[a, , c] = arr`           |
| `[a, ...rest]`       | 나머지를 배열로 묶기 | `[a, ...rest] = [1,2,3]`   |
| `[a, b] = [b, a]`    | 값 스왑        | `[x, y] = [y, x]`          |
| `[...copy] = arr`    | 배열 복사       | `const copy = [...arr]`    |
| `[...arr1, ...arr2]` | 배열 병합       | `const all = [...a, ...b]` |

<div style="margin-top:100px;"></div>






# 객체 구조분해할당

### 두가지 방법이 있다
```js
const { x, y } = obj; // 이러면 구조분해로 정확히 인식됨 

({ x, y } = obj); // 괄호로 감싸면 '이건 블럭 아님'이라고 알려줌
```

* 배열처럼 그냥 적으면 안된다. 앞에 const , let 으로 붙여줘야한다.
* 그렇지 않으면 JS가 블록으로 오해하기 때문이다.
* 변수선언이 하고싶지 않으면 밑부분 코드처럼 소괄호로 감싸면 된다.

<div style="margin-top:100px;"></div>


### 객체 스프레드 문법에서의 서순

```js
// user 객체를 복사하면서 age만 31로 덮어쓴 새 객체 updatedUser를 만드시오

const user = {
    name: '승현',
    age: 30
};


let updatedUser = {...user,age:31};
```

✔️ ...user는 객체 복사  
✔️ 뒤에 붙은 age: 31은 기존 age: 30을 덮어쓴다  
✔️ 순서가 중요하다 → 나중에 오는 속성이 우선권을 가짐  
✔️ 원본 user는 절대 손상되지 않음

```js
const user = {
  name: '승현',
  age: 30
};

let updatedUser = {
  age: 31,
  ...user
};

console.log(updatedUser);
```

만약 순서가 이렇게 ...user 보다 age:31이 앞에온다면

결과는 
```js
{ age: 30, name: '승현' } ✅
```
이렇게 나온다.

<div style="margin-top:100px;"></div>


# 객체 구조분해 할당 vs 스프레드 연산의 차이

| 문법                            | 하는 일                       | 예시                                     | 설명                              |
| ----------------------------- | -------------------------- | -------------------------------------- | ------------------------------- |
| `const { key: newVar } = obj` | 객체에서 값을 **꺼내서 변수에 저장**     | `const { stuName: name } = student`    | `stuName` 값을 `name`이라는 변수에 저장함  |
| `{ ...obj, key: value }`      | 기존 객체를 **복사 + 일부 속성 덮어쓰기** | `const updated = { ...user, age: 31 }` | `user` 객체 복사 후 `age`를 새 값으로 교체함 |



### 구조분해 하면서 이름바꾸기 와 스프레드 연산하면서 프로퍼티 값 바꾸기 가 적는방식이 똑같아서 헷갈릴 위험이 있다.

<div style="margin-top:100px;"></div>

## 🎯둘의 문법의 차이


| 목적                     | 문법 위치      | 예시                               | 의미                             |
| ---------------------- | ---------- | -------------------------------- | ------------------------------ |
| **구조분해 하면서 변수 이름 바꾸기** | 구조분해 할당 안쪽 | `const { stuName: name } = obj;` | `obj.stuName` 값을 `name` 변수에 저장 |
| **스프레드하면서 속성 값 덮어쓰기**  | 객체 리터럴 안쪽  | `{ ...user, age: 31 }`           | user의 age 값을 31로 덮어씀           |


<div style="margin-top:100px;"></div>

## ❗ 헷갈렸던 부분:

객체 리터럴 안에서 `user.settings`는 에러라고 했는데,
왜 `...user.settings`는 괜찮은 거지?

### ✅ 깨달은 포인트:
👉 키로 쓸 땐 `user.settings:` → ❌ 문법 오류 (식별자 아님)
👉 값으로 쓸 땐 `user.settings` → ✅ 문제 없음 (값은 표현식 OK)

✔ 즉, dot(.) 문법은 **값으로 쓸 땐 되고**, **키로 쓸 땐 안 된다**

<div style="margin-top:100px;"></div>

### 객체에서의 키와 값
```js
const user = {
  name: '승현',   // ← name은 키, '승현'은 값
  age: 30         // ← age는 키, 30은 값
};
```


<div style="margin-top:100px;"></div>

### 까먹지 말것

* ✔ 키를 변수로 쓰고 싶으면 → 꼭 대괄호!
* ✔ [key]: value → key가 'job'이면 → job: value로 들어감
* ✔ 스프레드랑 같이 쓰면 → 기존 값 덮어쓰기 가능!

### 예시

```js
const key = 'job';
const value = '프론트엔드';

const updated = { ...user, [key]: value };
```


<div style="margin-top:100px;"></div>





# 🚀 배열 고차함수


### 대표적인 고차함수들

| 함수          | 하는 일                    | 실전 사용 예         |
| ----------- | ----------------------- | --------------- |
| `forEach()` | 배열 순회 (반복)              | 콘솔 출력, DOM 조작 등 |
| `map()`     | 각 요소를 변형해서 새 배열 생성      | 이름 변환, 포맷팅 등    |
| `filter()`  | 조건에 맞는 요소만 남긴 새 배열 생성   | 특정 조건만 필터링      |
| `reduce()`  | 누적값 계산해서 하나의 값 도출       | 합계, 평균, 객체 묶기 등 |
| `find()`    | 조건을 만족하는 **첫 번째 요소** 찾기 | 사용자 찾기 등        |




### map

map 함수에서 블록을 사용할 경우 return을 반드시 적어줘야한다 map 함수에서는 return을 만나도 함수가 종료되지않는다.

이유: return은 항상 **현재** 함수만 종료시킨다. 즉 map함수의 return은 map함수 안에있는 callback함수를 종료시키는것이고 map함수는 callback 함수를 반복 시키는 함수이다.