# 타이머 함수
<div style="margin-top:70px;"></div>

## 동기(Synchronous)와 비동기(Asynchronous)
<div style="margin-top:70px;"></div>

### 동기(Synchronous): 
* 코드가 순차적으로 실행되며, 이전 코드가 완료되어야 다음 코드가 실행됩니다. 예를 들어, console.log("A"); console.log("B");는 항상 "A"가 출력된 후 "B"가 출력된다.

### 비동기(Asynchronous): 
* 코드가 즉시 실행되지 않고 일정 시간이 지난 후에 실행되거나, 특정 조건이 만족될 때 실행됩니다. JavaScript의 setTimeout과 같은 타이머 함수는 비동기 함수의 한 예.

<div style="margin-top:100px;"></div>

## ## 1.2 타이머 함수의 필요성과 용도

타이머 함수는 다음과 같은 경우에 유용하게 사용됩니다.

<div style="margin-top:70px;"></div>

### 1. **지연된 작업 수행**

- **예시**: 버튼을 클릭한 후 일정 시간 후에 메시지를 표시하거나, 애니메이션을 일정 시간 후에 시작할 때 사용할 수 있습니다.
- 이를 위해 **`setTimeout` 함수**를 사용합니다.

### 2. **주기적인 작업 반복**

- **예시**: 시계 애플리케이션처럼 일정 시간마다 화면을 업데이트하거나, 실시간 데이터(예: 주식 시세, 날씨 정보)를 주기적으로 받아와야 할 때 사용합니다.
- 이를 위해 **`setInterval` 함수**를 사용합니다.

### 3. **UI 업데이트**

- **예시**: 모달 팝업을 몇 초 후에 자동으로 닫거나, 알림 메시지를 일정 시간 후에 사라지게 할 때 타이머를 사용할 수 있습니다.

<div style="margin-top:100px;"></div>

## setTimeout 함수

<div style="margin-top:50px;"></div>

## 2.1 `setTimeout`의 기본 사용법

<div style="margin-top:50px;"></div>

`setTimeout` 함수의 기본 구문은 다음과 같습니다:

```jsx
setTimeout(함수, 지연시간);

```

- **함수**: 지연 시간이 지난 후 실행할 함수입니다.
- **지연 시간**: 함수를 지연시킬 시간(밀리초 단위)입니다. 1초는 1000밀리초입니다.

```jsx
setTimeout(() => {
  console.log("Hello, World!");
}, 2000);
```
<div style="margin-top:50px;"></div>

## 2.2 지연 시간 설정 및 함수 호출

`setTimeout`을 통해 함수를 호출할 때 지연 시간을 설정할 수 있습니다. 지연 시간이 0이면 즉시 실행되지만, 브라우저에 따라 실제 실행까지 약간의 지연이 있을 수 있습니다.

```js
function showMessage() {
  console.log("3초 후에 실행되었습니다.");
}

// 3초(3000ms) 후에 showMessage 함수를 호출합니다.
setTimeout(showMessage, 3000);

```

```js
let timeout = null;

timeout = setTimeout()
```

<div style="margin-top:50px;"></div>

### 이렇게 변수를 따로 지정해주면 그것이 setTimeout의 id이다. clearTimeout 할때 꼭 필요하다.




### 밀리초 단위이다 3000은 3초


<div style="margin-top:70px;"></div>

## 2.3 `clearTimeout`을 통한 타이머 취소

`setTimeout`은 타이머 ID를 반환하는데, 이 ID를 사용하여 `clearTimeout`을 통해 **예약된 타이머를 취소**할 수 있습니다. 이는 타이머를 실행하기 전에 조건에 따라 실행을 막고자 할 때 유용합니다.

### 예시: 타이머 취소하기

```jsx
// 5초 후에 실행될 타이머 설정
const timerId = setTimeout(() => {
  console.log("이 메시지는 출력되지 않습니다.");
}, 5000);

// 타이머 취소
clearTimeout(timerId);

```

위 코드에서 `clearTimeout(timerId);`는 `setTimeout`으로 설정된 타이머를 취소하여, **5초 후에 실행될 예정이었던 함수가 호출되지 않도록** 합니다.

<div style="margin-top:50px;"></div>

### 주의사항

* setTimeout이 연속으로 입력되면 이벤트가 꼬일 수 있다.
* 예를 들어 setTimeout이 설정된 버튼을 계속 누른다거나..
* 그런경우 clear 되는 과정이 없으면 타이머는 계속 쌓이면서 돌아간다.

<div style="margin-top:80px;"></div>

# 3. `setInterval` 함수

`setInterval` 함수는 JavaScript에서 **지정한 시간 간격마다 반복적으로 실행되는 작업을 예약**하는 데 사용됩니다. `setInterval`은 반복적으로 실행해야 하는 작업에 적합합니다.

---
<div style="margin-top:50px;"></div>

## 3.1 `setInterval`의 기본 사용법

`setInterval` 함수의 기본 구문은 다음과 같습니다:

```jsx
setInterval(함수, 간격시간);

```

- **함수**: 반복 실행할 함수입니다.
- **간격 시간**: 함수를 반복 실행할 시간 간격(밀리초 단위)입니다. 1초는 1000밀리초입니다.

<div style="margin-top:50px;"></div>

```js
function showTime() {
  const now = new Date();
  console.log(`현재 시간: ${now.toLocaleTimeString()}`);
}

// 1초마다 현재 시간을 출력
setInterval(showTime, 1000);

```

### 이런식으로 1초마다 현재시간을 반영해주는 시계를 만들수도 있다.

<div style="margin-top:50px;"></div>

## 3.3 `clearInterval`을 통한 반복 취소


`setInterval`은 타이머 ID를 반환하며, 이 ID를 사용하여 `clearInterval`을 호출하면 **반복 예약을 취소**할 수 있습니다. 이를 통해 필요 없는 반복 작업을 중간에 중단할 수 있습니다.

<div style="margin-top:50px;"></div>

### 예시: 타이머 취소하기

```jsx
// 1초마다 메시지를 출력하는 타이머 설정
const intervalId = setInterval(() => {
  console.log("이 메시지는 1초마다 출력됩니다.");
}, 1000);

// 5초 후에 반복 취소
setTimeout(() => {
  clearInterval(intervalId);
  console.log("반복이 취소되었습니다.");
}, 5000);

```

위 코드에서는 `clearInterval(intervalId);`를 사용하여 **5초 후에 반복을 취소**합니다. 결과적으로 `"이 메시지는 1초마다 출력됩니다."`가 5초 동안 출력되고, 이후에는 더 이상 출력되지 않습니다.


<div style="margin-top:50px;"></div>

### 요약

- `setInterval`은 **특정 시간 간격마다 반복적으로 실행**할 작업을 예약하는 함수입니다.
- 타이머 ID를 반환하며, `clearInterval`을 사용해 반복을 중단할 수 있습니다.
- **주기적인 작업**이나 **타이머 기반 애니메이션**에 적합하며, 필요에 따라 반복을 중단할 수 있습니다.

<div style="margin-top:100px;"></div>

# setInterval을 이용한 스톱워치 만들기

```js
 intervalId = setInterval(() => {
                elapsedTime += 10;
                formatElapsedTime();
                $pause.disabled = false;
                $pause.textContent = '일시정지';
                $start.disabled = true;
                if($display.textContent ===`00:10:00`){
                    $HOS.style.opacity = `1`;
                }

            }, 10);
```

### 인터벌이 10밀리초 마다 반복되는데, 이 반복주기마다 `elapsedTime += 10;` 이렇게 변수에 값을 저장하면
### 10밀리초마다 변수에 10의 값이 쌓이게 된다. 이것을 이용해 스톱워치 처럼 누적시간을 계산하는 기능 추가가 가능

<div style="margin-top:100px;"></div>

### 00:00:00 단위 출력법

```js
const minutes = String(Math.floor(elapsedTime / 60000)).padStart(2, "0");// 2자리로 맞춰주고 모자라면 앞에 0, 문자열에만 사용가능
            const seconds = String(Math.floor((elapsedTime % 60000) / 1000)).padStart(2, "0");
            const millisecond = String(Math.floor((elapsedTime % 1000) / 10)).padStart(2, '0');

    // 지속적인 경과시간을 화면에 렌더링
     $display.textContent = `${minutes}:${seconds}:${millisecond}`;       
```

### 이렇게 밀리초 단위로 쌓인 시간값을 시간 분 초 단위로 환산하여 출력이 가능하다.
