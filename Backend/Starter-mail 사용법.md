
# spring-boot-starter-mail 사용법 및 워크플로우

`spring-boot-starter-mail`은 스프링 부트 프로젝트에서 이메일 전송 기능을 구현하는 가장 간단한 방법을 제공하는 라이브러리이다.

이 문서는 해당 라이브러리의 핵심 사용법과 전체적인 워크플로우를 3단계로 나누어 설명한다.

---

## 1. 의존성 추가 (기능 설치)

먼저, 프로젝트의 `build.gradle` 파일에 아래의 의존성을 추가하여 이메일 전송 기능을 설치한다.

```groovy
// build.gradle

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-mail'
}
````

이 설정 하나로 이메일 발송에 필요한 모든 라이브러리가 프로젝트에 포함된다.

-----

## 2\. 이메일 계정 설정

다음으로 `src/main/resources/application.properties` 파일에 이메일 발송에 사용할 서버 및 계정 정보를 설정한다.

스프링 부트는 이 설정 정보를 읽어 `JavaMailSender` 객체를 자동으로 생성하고 구성한다.

**Gmail SMTP 서버 사용 예시:**

```yml
spring:
  mail:
    # 사용할 이메일 서버 정보
    host: smtp.gmail.com
    port: 587
    
    # 서버에 접속할 계정 정보
    username: 사용자계정@gmail.com
    password: 사용자계정의 앱 비밀번호 # 보안 강화를 위해 실제 비밀번호 대신 앱 비밀번호 사용을 권장한다.
    
    # 추가 SMTP 설정
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true

```

-----

## 3\. 코드 작성 및 사용

설정이 완료되면, 서비스 로직에서 `JavaMailSender` 객체를 주입받아 이메일을 발송할 수 있다.

아래는 이메일 발송 기능을 담당하는 서비스 클래스의 예시 코드이다.

**`EmailService.java`**

```java
import lombok.RequiredArgsConstructor;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class EmailService {

    // application.properties의 설정을 기반으로 스프링이 자동 생성한 객체
    private final JavaMailSender mailSender;

    /**
     * 지정된 주소로 간단한 텍스트 이메일을 발송한다.
     *
     * @param to      받는 사람의 이메일 주소
     * @param subject 이메일 제목
     * @param text    이메일 본문
     */
    public void sendEmail(String to, String subject, String text) {
        // 1. 이메일 메시지 양식 생성
        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(text);

        // 2. 생성된 메시지를 기반으로 이메일 발송
        mailSender.send(message);
    }
}
```

### 워크플로우 요약

1.  **의존성 추가**: `build.gradle`에 `spring-boot-starter-mail`을 추가한다.
2.  **정보 설정**: `application.properties`에 SMTP 서버 및 계정 정보를 입력한다.
3.  **코드 구현**: `JavaMailSender`를 서비스 클래스에 주입하고, `send()` 메소드를 호출하여 이메일을 발송한다.

