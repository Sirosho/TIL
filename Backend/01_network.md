# ✅ 인터넷 통신 요약 정리

## 1. 클라이언트와 서버
- 데이터를 요청하는 주체는 **클라이언트**이다.
- 데이터를 응답하는 주체는 **서버**이다.

<div style="margin-top:80px;"></div>

## 2. IP (Internet Protocol Address)
- 네트워크 상의 컴퓨터를 구분하기 위한 고유한 **주소**이다.
- 데이터를 담은 **패킷(packet)**에는 출발지 IP와 도착지 IP가 함께 포함된다.

<div style="margin-top:80px;"></div>

## 3. IP 프로토콜의 한계
1. **비연결성**: 상대가 준비되지 않았더라도 데이터를 전송함
2. **비신뢰성**: 패킷 손실 또는 순서가 바뀔 수 있음
3. **프로그램 구분 불가**: 동일한 컴퓨터 내 여러 프로그램을 구분할 수 없음

<div style="margin-top:80px;"></div>

## 4. TCP (Transmission Control Protocol)
- IP 프로토콜의 한계를 보완하기 위한 **신뢰성 있는 전송 프로토콜**이다.



### ✅ TCP 특징
- **연결성 보장**
- **데이터 전송 순서 보장**
- **정확한 데이터 전송 (재전송, 검증)**



### ✅ 3-way-handshake (3단계 연결 절차)
1. 클라이언트 → 서버: SYN (접속 요청)
2. 서버 → 클라이언트: SYN + ACK (요청 수락 및 확인)
3. 클라이언트 → 서버: ACK (수락 확인 후 데이터 전송 시작)

<div style="margin-top:80px;"></div>

## 5. PORT (포트 번호)
- 한 컴퓨터 내의 여러 프로그램(프로세스)을 **식별하기 위한 번호**이다.
- 하나의 IP 주소에 여러 포트를 통해 프로그램 구분 가능

<div style="margin-top:80px;"></div>

### ✅ 포트 번호 범위
- **0 ~ 65535번**까지 존재
- 이 중,
  - **0 ~ 1023**: Well-known Port (예약 포트, 시스템에서 사용)
  - **1024 ~ 49151**: Registered Port (등록된 일반 포트)
  - **49152 ~ 65535**: Dynamic/Private Port (임시 또는 사용자 지정용)

<div style="margin-top:80px;"></div>

### ✅ 주요 포트 번호 예시
| 포트 번호 | 설명              |
|-----------|-------------------|
| 20, 21    | FTP               |
| 22        | SSH               |
| 23        | Telnet            |
| 25        | SMTP (메일 전송) |
| 53        | DNS               |
| 80        | HTTP              |
| 443       | HTTPS             |

<div style="margin-top:80px;"></div>

## 6. DNS (Domain Name System)
- 숫자로 된 IP 주소를 사람이 이해하기 쉬운 **도메인 이름**으로 변환하는 시스템
- 예시: `www.google.com` → `142.250.206.100`
- 전화번호 대신 **연락처 이름**을 기억하는 것과 유사한 개념



<div style="margin-top:80px;"></div>

# ✅ HTTP 요청과 URI 정리

<div style="margin-top:80px;"></div>

## 1. HTTP 요청 개요
- 클라이언트(웹 브라우저)가 서버에 요청을 보낼 때 사용하는 프로토콜은 **HTTP**이다.
- 요청의 예: 로그인 요청, 게시글 조회, 첨부파일 다운로드 등
- 요청 시 원하는 자원을 지정하기 위해 사용하는 것이 **URI**이다.

---

<div style="margin-top:80px;"></div>

## 2. URI (Uniform Resource Identifier)
- **자원을 식별하는 통합된 방법**
- URI는 **URL**과 **URN**으로 나뉜다.
  - URL: 자원의 **위치**를 나타냄 (예: `https://google.com`)
  - URN: 자원의 **이름**을 나타냄 (잘 사용되지 않음)
- 실무에서는 URI ≒ URL 이라고 보면 된다.

---

<div style="margin-top:80px;"></div>

## 3. URL 구성요소

> 형식: `scheme://[userinfo@]host[:port][/path][?query][#fragment]`  
예시: `https://search.naver.com:443/search.naver?query=hello&fbm=0`

<div style="margin-top:80px;"></div>

| 구성 요소 | 설명 |
|-----------|------|
| **scheme** | 사용하는 프로토콜을 명시 (예: http, https, ftp, mysql 등) |
| **userinfo** | 사용자 인증정보 (거의 사용되지 않으며 생략 가능) |
| **host** | 서버 주소 (IP 주소 또는 도메인명) |
| **port** | 서버의 포트번호 (기본값: http=80, https=443) |
| **path** | 서버 자원의 경로 (예: `/user/login`) |
| **query** | 클라이언트가 서버에 전달하는 데이터 (형태: `key=value`, `?`로 시작, `&`로 구분) |
| **fragment** | 문서 내부의 특정 위치 식별 (서버에는 전달되지 않음, 북마크 용도 등) |

---

<div style="margin-top:80px;"></div>

## 4. query string 예시


`/coffee/order?type=latte&shot=3&milk=low-fat&size=grande`


- 위 요청은 서버에 아래와 같은 데이터를 전달한다:
  - `type=latte`
  - `shot=3`
  - `milk=low-fat`
  - `size=grande`



<div style="margin-top:80px;"></div>


# ✅ HTTP (HyperText Transfer Protocol)

<div style="margin-top:50px;"></div>

## 1. HTTP란?
- **HTML 문서, 텍스트, 이미지, 음성, 영상, 파일** 뿐 아니라  
  **JSON, XML** 등의 데이터까지 전송 가능한 애플리케이션 계층 프로토콜이다.

---

<div style="margin-top:80px;"></div>

## 2. HTTP 특징

### 1️⃣ 클라이언트-서버 구조
- 클라이언트가 요청을 보내고, 서버는 응답을 돌려준다.
- **하나의 요청**에 대해 **하나의 응답**이 생성된다.



### 2️⃣ 무상태성 (Stateless)
- 서버는 클라이언트의 상태를 **기억하지 않는다**.
- 매 요청마다 필요한 정보를 **모두 포함**해서 보내야 한다.
- ✅ 장점: 특정 서버에 종속되지 않아 **수평 확장(스케일 아웃)**이 쉬움
- ⚠️ 단점: 로그인 등 상태 유지가 필요한 기능이 불편함
- ➕ 보완책: **쿠키, 세션, 토큰** 등 상태 유지 기술 사용


### 3️⃣ 비연결성 (Connectionless)
- 요청 후 응답이 끝나면 **TCP 연결을 즉시 종료**한다.
- 지속 연결을 유지하지 않기 때문에 **네트워크 자원을 절약**할 수 있다.
- ✅ 예: 수천 명이 동시에 접속해도 불필요한 리소스 낭비가 없음


### 4️⃣ HTTP 메시지 기반 통신
- HTTP는 **요청(Request)**과 **응답(Response)**을 **메시지 형태**로 주고받는다.
- 이 메시지는 항상 **표준 형식**을 따른다.



### 5️⃣ 단순성 및 확장 가능성

* HTTP는 구조가 **단순하고 명확**하다.
* 새로운 메서드, 헤더, 콘텐츠 타입 등을 추가하기 **매우 유연**하다.

---



<div style="margin-top:80px;"></div>

### 📦 HTTP 메시지 구조 예시

```

## 요청 메시지:

GET /user HTTP/1.1
Host: example.com
Header1: value1
Header2: value2

(Body 생략 가능)

## 응답 메시지:

HTTP/1.1 200 OK
Header1: value1
Header2: value2

<body>응답 데이터</body>
```

<div style="margin-top:80px;"></div>

## ✅ HTTP 핵심 요약

| 특징          | 설명                           |
| ----------- | ---------------------------- |
| 클라이언트-서버 구조 | 요청과 응답의 명확한 역할 구분            |
| 무상태성        | 상태를 저장하지 않음 (→ 쿠키/세션 등으로 보완) |
| 비연결성        | 요청 후 TCP 연결 종료 → 자원 효율적      |
| 메시지 기반 통신   | 표준 메시지 포맷으로 데이터 송수신          |
| 단순성/확장성     | 구조가 간단하고 확장이 쉬움              |


<div style="margin-top:80px;"></div>

# ✅ HTTP 헤더 & 상태 코드 요약 정리

<div style="margin-top:80px;"></div>

## 1. HTTP 헤더 개념

- HTTP 헤더는 요청(Request)과 응답(Response)에 포함되는 **메타데이터 정보**이다.
- **Key-Value 쌍**으로 구성되며, 요청/응답의 동작 방식, 포맷, 인증, 캐시 등을 제어한다.


<div style="margin-top:80px;"></div>

## 2. 요청 헤더 (Request Headers)

| 헤더             | 설명 |
|------------------|------|
| Accept           | 클라이언트가 처리 가능한 콘텐츠 유형(MIME 타입)을 명시 |
| Accept-Encoding  | 수신 가능한 압축 인코딩 형식 지정 (gzip 등) |
| Accept-Language  | 선호하는 언어 설정 |
| Authorization    | 인증 토큰 등 인증 정보 포함 |
| Cache-Control    | 요청 캐시 정책 제어 |
| Content-Length   | 요청 본문의 바이트 크기 |
| Content-Type     | 요청 본문의 콘텐츠 형식 지정 |
| User-Agent       | 브라우저, OS 등 클라이언트 정보 전달 |


<div style="margin-top:80px;"></div>

## 3. 응답 헤더 (Response Headers)

| 헤더               | 설명 |
|--------------------|------|
| Cache-Control      | 응답 캐시 정책 제어 |
| Content-Disposition| 파일 다운로드 시 파일 이름 지정 |
| Content-Encoding   | 응답 본문의 인코딩 형식 (gzip 등) |
| Content-Length     | 응답 본문의 바이트 크기 |
| Content-Type       | 응답 데이터의 콘텐츠 형식 |
| Date               | 응답 생성 시간 |
| ETag               | 리소스의 고유 식별자 (조건부 요청, 캐시 제어) |
| Expires            | 리소스 만료 시간 |
| Last-Modified      | 리소스 최종 수정 시간 |
| Server             | 응답을 생성한 서버의 정보 |
| Set-Cookie         | 쿠키 설정 정보 |
| WWW-Authenticate   | 인증 방식 명시 (401 응답 시 사용) |


<div style="margin-top:80px;"></div>


## 4. HTTP 상태 코드 (Status Code)

HTTP 상태 코드는 서버가 클라이언트 요청에 대해 응답한 **결과 코드**이다.  
총 **3자리 숫자**로 이루어지며, 첫 번째 숫자에 따라 **범주(category)**가 나뉜다.

<div style="margin-top:80px;"></div>

### ✅ 상태 코드 범주

| 범주  | 설명 |
|-------|------|
| 1xx   | 정보: 요청 처리 중 (드물게 사용됨) |
| 2xx   | 성공: 요청 정상 처리 |
| 3xx   | 리다이렉션: 요청을 완료하려면 추가 작업 필요 |
| 4xx   | 클라이언트 오류: 잘못된 요청 |
| 5xx   | 서버 오류: 서버 내부 문제로 요청 처리 실패 |

---

### 📘 1xx (정보)

- **100 Continue**: 클라이언트가 요청을 계속 진행해도 됨
- **101 Switching Protocols**: 프로토콜 전환 승인


### ✅ 2xx (성공)

| 코드 | 설명 |
|------|------|
| 200 OK           | 요청 정상 처리 완료 |
| 201 Created      | 새 리소스 생성 성공 |
| 204 No Content   | 성공했지만 응답 본문 없음 |


### 🔁 3xx (리다이렉션)

| 코드 | 설명 |
|------|------|
| 300 Multiple Choices | 여러 응답 가능성 존재 |
| 301 Moved Permanently | 리소스가 영구적으로 이동됨 |
| 302 Found            | 일시적 리다이렉션 |


### ⚠️ 4xx (클라이언트 오류)

| 코드 | 설명 |
|------|------|
| 400 Bad Request      | 잘못된 요청 형식 |
| 401 Unauthorized     | 인증 필요 |
| 403 Forbidden        | 접근 권한 없음 |
| 404 Not Found        | 리소스를 찾을 수 없음 |
| 429 Too Many Requests| 요청이 너무 많음 (Rate Limit 초과) |


### 💥 5xx (서버 오류)

| 코드 | 설명 |
|------|------|
| 500 Internal Server Error | 서버 내부 오류 |
| 502 Bad Gateway           | 게이트웨이에서 잘못된 응답 수신 |
| 503 Service Unavailable   | 서버 일시적 과부하 또는 점검 중 |
| 504 Gateway Timeout       | 상위 서버 응답 시간 초과 |


<div style="margin-top:80px;"></div>

## ✅ 마무리 요약

- **HTTP 헤더**는 요청과 응답의 속성을 정의하는 중요한 메타 정보이다.
- **HTTP 상태 코드**는 클라이언트가 요청한 결과에 대한 서버의 상태를 명확히 표현한다.
- 이 두 개념은 **HTTP 통신의 핵심**이며, 정확한 처리를 위해 반드시 이해해야 하는 요소이다.
