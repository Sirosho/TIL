# 🌐 Spring CORS 개념 및 CorsRegistry 전역 설정 정리

<div style="margin-top:80px;"></div>

## ✅ 1. CORS란?

CORS(Cross-Origin Resource Sharing)는 **다른 출처(Origin)**에서 오는 HTTP 요청을  
**브라우저가 기본적으로 차단**하는 보안 정책이다.  
서버는 CORS 설정을 통해 특정 Origin에서 오는 요청을 **명시적으로 허용**해야 한다.


---

### 🔹 Origin이란?

Origin은 요청을 보낸 웹사이트의 **출처**를 말하며 다음 3가지 요소로 구성된다:

| 구성 요소 | 예시 |
|-----------|------|
| 프로토콜  | `http` 또는 `https` |
| 도메인    | `localhost`, `example.com` |
| 포트      | `3000`, `8080`, `443` 등 |

이 중 하나라도 다르면 다른 Origin으로 간주되어 **브라우저에서 CORS 검사를 수행**한다.

---

<div style="margin-top:80px;"></div>

## ⚠️ 2. CORS 에러 예시

```text
Access to fetch at 'http://localhost:8080/api/data' from origin 'http://localhost:3000'
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present...
````

* 프론트엔드와 백엔드의 Origin이 다를 경우 발생
* 서버에서 해당 Origin을 허용하지 않았을 때 발생

---

<div style="margin-top:80px;"></div>

## 🔧 3. Spring Boot에서의 전역 CORS 설정

전역 CORS 설정은 `WebMvcConfigurer`를 구현하여 `CorsRegistry`를 통해 지정한다.

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**") // 이 경로에 대해서만
                .allowedOrigins("http://localhost:3000") // 이 Origin에서 온 요청만 허용
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS") // 허용할 HTTP 메서드
                .allowedHeaders("*") // 모든 요청 헤더 허용
                .exposedHeaders("Authorization", "Content-Disposition") // 응답 헤더 노출
                .allowCredentials(true) // 쿠키/인증 정보 허용
                .maxAge(3600); // preflight 결과 캐시 (초)
    }
}
```

---

<div style="margin-top:80px;"></div>

## 🧩 CorsRegistry 주요 메서드 정리

| 메서드                                | 설명                                                       |
| ---------------------------------- | -------------------------------------------------------- |
| `addMapping(String pathPattern)`   | CORS를 허용할 **서버 경로** 지정 (예: `/api/**`, `/**`)             |
| `allowedOrigins(String...)`        | 요청을 허용할 **클라이언트 Origin** 지정 (예: `http://localhost:3000`) |
| `allowedOriginPatterns(String...)` | 와일드카드 Origin 허용 (예: `https://*.example.com`)             |
| `allowedMethods(String...)`        | 허용할 HTTP 메서드 (`GET`, `POST`, `OPTIONS` 등)                |
| `allowedHeaders(String...)`        | 허용할 요청 헤더 (기본은 `*`)                                      |
| `exposedHeaders(String...)`        | 응답 시 클라이언트가 읽을 수 있는 헤더                                   |
| `allowCredentials(boolean)`        | 쿠키, 인증정보 포함 허용 (`true`이면 `*` Origin 불가)                  |
| `maxAge(long seconds)`             | preflight 결과를 캐시할 시간 (초)                                 |

---

<div style="margin-top:80px;"></div>

## 🛡️ 4. allowedOrigins와 addMapping 차이

| 메서드                                       | 기준           | 설명                        |
| ----------------------------------------- | ------------ | ------------------------- |
| `addMapping("/api/**")`                   | **서버 입장**    | 어떤 경로에 대해 CORS를 허용할지 지정   |
| `allowedOrigins("http://localhost:3000")` | **클라이언트 입장** | 어떤 웹사이트에서 들어온 요청을 허용할지 지정 |

---

<div style="margin-top:80px;"></div>

## ✅ 요약

* CORS는 **브라우저가 요청 출처를 검사하고** 서버가 허용하면 통과된다.
* Spring에서는 `CorsRegistry`를 통해 **전역 CORS 정책을 설정**할 수 있다.
* `allowedOrigins`는 클라이언트의 주소창 URL (Origin 기준),
  `addMapping`은 내 서버의 경로 기준이다.
