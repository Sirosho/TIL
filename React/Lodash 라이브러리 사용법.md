# Lodash란?

Lodash는 **자바스크립트 유틸리티 라이브러리**로, 배열, 객체, 문자열 등 데이터 구조를 쉽고 효율적으로 다룰 수 있도록 다양한 기능을 제공한다. 복잡한 코드를 간결하고 읽기 쉬운 한 줄로 만들어주는 경우가 많아 '자바스크립트계의 맥가이버 칼'이라고도 불린다.

## **주요 특징**

  * **일관성**: 브라우저마다 조금씩 다른 동작을 표준화하여 일관된 결과를 보장한다.
  * **편의성**: `map`, `filter`, `reduce`와 같은 기본 함수는 물론, `cloneDeep`, `debounce`, `throttle` 등 매우 유용한 고급 기능들을 제공한다.
  * **모듈성**: 필요한 함수만 개별적으로 불러와 사용할 수 있어 프로젝트의 용량을 최적화하기 좋다.
  * **성능**: 내부적으로 고도로 최적화되어 있어 직접 구현하는 것보다 빠른 성능을 보이는 경우가 많다.

-----

<div style="margin-top:100px;"></div>

## Vite 프로젝트에 Lodash 설치 및 사용법

Vite 환경에서 Lodash를 설치하고 사용하는 방법은 매우 간단하다.

<div style="margin-top:100px;"></div>

### **1. 패키지 설치**

터미널을 열고 프로젝트 경로에서 아래 명령어 중 하나를 실행하여 Lodash를 설치한다.

**npm 사용 시**

```bash
npm install lodash
```

**yarn 사용 시**

```bash
yarn add lodash
```

**pnpm 사용 시**

```bash
pnpm add lodash
```

<div style="margin-top:100px;"></div>

## **2. 컴포넌트에서 사용하기**

설치가 완료되면, 스크립트 파일 상단에서 `import` 키워드를 사용하여 Lodash를 불러온 후 사용하면 된다. 보통 `_` (언더스코어)라는 이름으로 불러오는 것이 관례이다.

```javascript
// 예: src/components/MyComponent.vue 또는 src/App.jsx

import _ from 'lodash';

// 원본 객체
const originalObject = {
  name: 'John',
  details: {
    age: 30,
    hobbies: ['reading', 'music']
  }
};

// lodash의 cloneDeep을 사용한 깊은 복사
const copiedObject = _.cloneDeep(originalObject);

// 복사된 객체의 내부 값을 변경해도 원본은 영향을 받지 않음
copiedObject.details.age = 31;
copiedObject.details.hobbies.push('sports');

console.log('원본 객체:', originalObject.details.age); // 출력: 30
console.log('복사된 객체:', copiedObject.details.age); // 출력: 31
```

<div style="margin-top:100px;"></div>

## 💡 **번들 사이즈 최적화**
프로젝트 전체에서 몇 가지 함수만 사용한다면, 라이브러리 전체를 불러오는 대신 필요한 함수만 개별적으로 불러와 번들 사이즈를 줄일 수 있다.

```javascript
// 필요한 함수만 개별적으로 가져오기
import cloneDeep from 'lodash/cloneDeep';
import debounce from 'lodash/debounce';

const copiedObject = cloneDeep(originalObject);
const debouncedSearch = debounce(() => { /* ... */ }, 300);
```

-----

<div style="margin-top:100px;"></div>

## 자주 사용되는 주요 Lodash 메서드

<div style="margin-top:100px;"></div>

### 🗂️ **배열 (Array) 관련 메서드**

| 메서드 (Method) | 설명 | 예시 (Code Example) |
| :--- | :--- | :--- |
| **`_.chunk(array, [size=1])`** | 배열을 `size` 크기만큼씩 잘라 새로운 2차원 배열로 만든다. UI에서 여러 아이템을 행마다 일정 개수씩 보여줄 때 특히 유용하다. | `_.chunk(['a', 'b', 'c', 'd'], 2);`\<br\>➡️ `[['a', 'b'], ['c', 'd']]` |
| **`_.uniqBy(array, iteratee)`** | 특정 기준(`iteratee`)을 적용하여 중복을 판단하고 제거한다. 객체 배열에서 특정 키 값을 기준으로 중복을 제거할 때 자주 사용된다. | `_.uniqBy([{ 'x': 1 }, { 'x': 2 }, { 'x': 1 }], 'x');`\<br\>➡️ `[{ 'x': 1 }, { 'x': 2 }]` |
| **`_.flattenDeep(array)`** | 아무리 깊게 중첩된 배열이라도 모두 펼쳐서 1차원 배열로 만들어 준다. | `_.flattenDeep([1, [2, [3, [4]], 5]]);`\<br\>➡️ `[1, 2, 3, 4, 5]` |
| **`_.groupBy(collection, iteratee)`** | 컬렉션의 각 요소를 `iteratee` 함수가 반환하는 값을 기준으로 그룹화하여 객체로 만든다. 데이터를 카테고리별로 묶을 때 편리하다. | `_.groupBy(['one', 'two', 'three'], 'length');`\<br\>➡️ `{ '3': ['one', 'two'], '5': ['three'] }` |

<div style="margin-top:100px;"></div>

### 📦 **객체 (Object) 관련 메서드**

| 메서드 (Method) | 설명 | 예시 (Code Example) |
| :--- | :--- | :--- |
| **`_.cloneDeep(value)`** 🚀 | 값(객체, 배열 등)을 \*\*완벽하게 깊은 복사(Deep Copy)\*\*한다. 중첩된 객체나 배열까지 모두 새로운 참조로 복사하므로, 원본 데이터를 안전하게 보호할 수 있는 가장 확실한 방법이다. | `const original = { a: 1, b: { c: 2 } };`\<br\>`const copied = _.cloneDeep(original);`\<br\>`copied.b.c = 3; // 원본(original)에 영향 없음` |
| **`_.get(object, path, [defaultValue])`** | `object.a.b.c`와 같이 중첩된 객체의 속성에 안전하게 접근한다. 중간 경로가 존재하지 않아도 오류가 발생하지 않고 `undefined`나 지정한 `defaultValue`를 반환한다. | `const object = { 'a': [{ 'b': { 'c': 3 } }] };`\<br\>`_.get(object, 'a[0].b.c'); // 3`\<br\>`_.get(object, 'a.d.e', 'Not Found'); // 'Not Found'` |
| **`_.pick(object, [paths])`** | 객체에서 원하는 속성(`paths`)만 선택하여 새로운 객체를 만든다. API로 전송할 데이터를 정리하거나 필요한 필드만 추출할 때 유용하다. | `const object = { 'a': 1, 'b': '2', 'c': 3 };`\<br\>`_.pick(object, ['a', 'c']);`\<br\>➡️ `{ 'a': 1, 'c': 3 }` |
| **`_.isEqual(value, other)`** | 두 값(주로 객체나 배열)의 내용이 완전히 동일한지 \*\*깊은 비교(Deep Comparison)\*\*를 한다. 참조가 달라도 내부의 모든 속성과 값이 같다면 `true`를 반환한다. | `const obj1 = { a: { b: 1 } };`\<br\>`const obj2 = { a: { b: 1 } };`\<br\>`obj1 === obj2; // false`\<br\>`_.isEqual(obj1, obj2); // true` |

<div style="margin-top:100px;"></div>

### 🛠️ **유틸리티 (Utility) 함수**

| 메서드 (Method) | 설명 | 예시 (Code Example) |
| :--- | :--- | :--- |
| **`_.debounce(func, wait)`** ⭐ | 이벤트가 연속해서 발생할 때, 마지막 이벤트 이후 `wait` 시간 동안 추가 이벤트가 없으면 `func`를 **한 번만** 실행한다. **검색어 자동완성**처럼 사용자의 입력이 끝났을 때 API 요청을 보내는 경우에 필수적이다. | `// 윈도우 크기 조절이 멈추고 250ms 후에 실행`\<br\>`window.addEventListener('resize', _.debounce(() => { console.log('Resized!'); }, 250));` |
| **`_.throttle(func, wait)`** ⭐ | 이벤트가 아무리 자주 발생해도 `wait` 시간당 **최대 한 번만** `func`를 실행하도록 제어한다. **스크롤 이벤트**나 마우스 이동처럼 과도한 실행을 방지하여 성능을 최적화할 때 사용한다. | `// 스크롤 이벤트가 발생해도 1초에 한 번만 실행`\<br\>`window.addEventListener('scroll', _.throttle(() => { console.log('Scrolled!'); }, 1000));` |
| **`_.isEmpty(value)`** | 주어진 값이 비어있는지 확인한다. `null`, `undefined`는 물론, 내용이 없는 객체(`{}`), 배열(`[]`), 문자열(`""`)에 대해서도 `true`를 반환하여 유효성 검사에 유용하다. | `_.isEmpty(null); // true`\<br\>`_.isEmpty({}); // true`\<br\>`_.isEmpty([1, 2, 3]); // false` |

-----

<div style="margin-top:100px;"></div>


## 정리

Lodash는 간결하고 안정적인 코드를 작성하는 데 매우 강력한 도구이다. 비록 최신 JavaScript 문법(ES6+)이 많은 편의 기능을 자체적으로 제공하지만, `cloneDeep`, `debounce`, `isEqual`과 같이 Lodash만이 제공하는 독보적인 기능들은 여전히 실무에서 매우 유용하게 사용된다.