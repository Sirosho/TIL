# React Router - Params와 Request 핵심 정리

## Params (파라미터)
**URL에서 동적인 값을 추출하는 것이다.**

### URL Parameters
```javascript
// /user/:id → useParams()로 id 값을 가져온다
const { id } = useParams(); // /user/123 → id = "123"
```

### Query Parameters  
```javascript
// ?q=검색어&page=1 → useSearchParams()로 쿼리 값을 가져온다
const [searchParams] = useSearchParams();
const query = searchParams.get('q'); // "검색어"
```

## Request (요청 정보)
**현재 페이지의 위치와 네비게이션 관련 정보다.**

### 현재 위치 정보
```javascript
// useLocation()으로 현재 경로 정보를 가져온다
const location = useLocation();
// location.pathname → "/user/123"
// location.search → "?tab=profile" 
// location.state → 네비게이션 시 전달된 데이터
```

**요약:**
- **Params** = URL에서 값을 뽑아내는 것
- **Request** = 현재 페이지의 위치와 상태 정보




<div style="margin-top:100px;"></div>




# `params`를 쓸 수 있는 경우

**`params`는 라우터 경로에 동적 세그먼트(예: `:id`)가 포함되어 있을 때만 의미가 있으며, 해당 경로와 연결된 `loader`나 `action`에서 사용할 수 있다.** `params`의 역할은 "어떤 특정 데이터에 대한 요청인지"를 알려주는 것이다.

URL 주소에 변수가 포함된 페이지에 대한 작업을 할 때 사용한다.

  * **상세 페이지 데이터 로드**: `/events/e1` 경로의 `loader`에서 `e1` 값을 가져와 해당 이벤트 정보 요청
  * **데이터 수정**: `/events/e1/edit` 경로의 `action`에서 `e1` 값을 가져와 해당 이벤트를 수정 요청
  * **데이터 삭제**: `/events/e1` 경로에서 `DELETE` 메서드로 `action`을 호출하여 `e1` 이벤트 삭제

<!-- end list -->

```jsx
// 1. 라우터에 :eventId 라는 동적 경로가 설정되어 있다.
{
  path: '/events/:eventId',
  loader: eventDetailLoader, // 여기서 params.eventId 사용 가능
  action: deleteEventAction, // 여기서 params.eventId 사용 가능
}

// 2. loader 또는 action 함수에서 params를 받아 사용한다.
export async function eventDetailLoader({ params }) {
  const id = params.eventId; // URL에서 'e1' 같은 값을 추출
  const response = await fetch(`http://.../events/${id}`);
  // ...
}
```

-----

<div style="margin-top:100px;"></div>

# `request`를 쓸 수 있는 경우

**`request`는 `loader`나 `action` 함수가 실행될 때 항상 사용할 수 있다.** `request`의 역할은 "요청에 대한 상세한 내용"을 알려주는 것이다.

<div style="margin-top:100px;"></div>

## 1\. `loader` 안에서의 `request`

`loader`는 페이지를 '읽기(Read)' 위한 것이므로, `request` 객체는 주로 URL의 쿼리 파라미터(`?` 뒤에 오는 값)를 읽을 때 유용하다.

  * **페이지네이션**: `/events?page=2`에서 `page` 값을 읽어 2페이지 데이터 요청
  * **검색/필터링**: `/events?type=concert`에서 `type` 값을 읽어 'concert' 타입의 이벤트만 요청

<!-- end list -->

```javascript
export async function eventListLoader({ request }) {
  const url = new URL(request.url);
  const page = url.searchParams.get('page') || 1; // 쿼리 파라미터 'page' 값 추출

  const response = await fetch(`http://.../events?page=${page}`);
  // ...
}
```

<div style="margin-top:100px;"></div>

## 2\. `action` 안에서의 `request`

`action`은 데이터를 '쓰기(Write)' 위한 것이므로, `request` 객체는 폼(Form) 데이터와 요청 메서드를 읽을 때 핵심적인 역할을 한다.

  * **새로운 데이터 생성**: 사용자가 입력한 폼 데이터를 `request.formData()`로 읽어서 서버에 `POST` 요청
  * **기존 데이터 수정**: `request.method`가 `PUT`인지 확인하고, `request.formData()`로 수정된 데이터를 읽어서 서버에 `PUT` 요청

<!-- end list -->

```javascript
export async function manipulateEventAction({ request, params }) {
  // 요청 메서드 확인
  const method = request.method;

  // 폼 데이터 추출
  const formData = await request.formData();
  const eventData = {
    title: formData.get('title'),
    // ...
  };
  
  if (method === 'PUT') {
    // 수정 로직 실행
  } else {
    // 생성 로직 실행
  }
  // ...
}
```



`request`와 `params` 객체를 인자로 직접 받는 곳은 **`loader`와 `action` 함수가 유일하다.** React Router는 이 두 함수를 "데이터 처리"를 위한 특별한 함수로 취급하며, 라우팅이 발생할 때 필요한 모든 맥락 정보(`request`, `params`)를 담아 넘겨준다.


<div style="margin-top:100px;"></div>


# 컴포넌트와의 차이점

React 컴포넌트는 이 값들을 직접 받지 않으며, **훅(Hook)을 통해 간접적으로 접근한다.**

  - **`params`**: 컴포넌트에서는 `useParams()` 훅을 사용하여 접근한다.
  - **`request`**: `request` 객체 자체는 컴포넌트에서 직접 접근할 수 없다. 대신, `loader`가 `request`를 사용하여 가져온 **데이터**를 `useLoaderData()` 훅으로 받아 사용한다.

-----

<div style="margin-top:100px;"></div>

## 요약표

| 구분 | 접근 방식 | 비고 |
| :--- | :--- | :--- |
| **`loader` & `action` 함수** | **직접 인자로 받음**\<br\>`({ request, params })` | `request` 객체의 모든 정보(메서드, 폼 데이터 등)를 활용할 수 있다. |
| **React 컴포넌트** | **훅(Hook)을 통해 간접 접근** | `useParams()`로 `params`에 접근한다. `request` 객체 자체는 접근할 수 없다. |