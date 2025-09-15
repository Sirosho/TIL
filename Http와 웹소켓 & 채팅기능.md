# WebSocket과 HTTP 통신의 차이점 및 실제 채팅 시스템 구현 분석

## 1. HTTP vs WebSocket 기본 개념

### HTTP 방식 (기존 웹 통신)
HTTP는 **"요청-응답" 패턴**으로 동작합니다:
- 클라이언트가 요청을 보내면 → 서버가 응답을 보내고 → 연결 종료
- **단방향 통신**: 클라이언트만 먼저 말을 걸 수 있음
- **무상태(Stateless)**: 각 요청은 독립적이고, 서버는 이전 요청을 기억하지 않음
- **폴링이 필요**: 실시간 데이터가 필요하면 클라이언트가 계속 요청을 보내야 함

```
클라이언트 → 서버: "새 메시지 있나요?"
서버 → 클라이언트: "없습니다"
(연결 종료)

클라이언트 → 서버: "새 메시지 있나요?" (다시 요청)
서버 → 클라이언트: "있습니다. 메시지: 안녕하세요"
(연결 종료)
```

### WebSocket 방식 (실시간 통신)
WebSocket은 **"지속적인 연결"**을 유지합니다:
- 한 번 연결하면 연결이 계속 유지됨 (전화 연결과 비슷)
- **양방향 통신**: 클라이언트와 서버 모두 언제든 먼저 메시지를 보낼 수 있음
- **상태 유지**: 연결이 유지되므로 서버가 클라이언트 상태를 알 수 있음
- **실시간 통신**: 새 데이터가 생기면 즉시 전송 가능

```
클라이언트 ↔ 서버: 연결 유지 중...
서버 → 클라이언트: "새 메시지가 도착했습니다: 안녕하세요"
클라이언트 → 서버: "메시지 확인했습니다"
```

## 2. STOMP 프로토콜 개념

**STOMP (Simple Text Oriented Messaging Protocol)**는 WebSocket 위에서 동작하는 메시징 프로토콜입니다:

### 핵심 개념들

1. **구독(Subscribe)**: "이 주소의 알림을 받겠습니다"
   ```javascript
   client.subscribe('/topic/rooms/1', function(message) {
       console.log('받은 메시지:', message.body);
   });
   ```

2. **전송(Send)**: "이 주소로 메시지를 보냅니다"
   ```javascript
   client.send('/app/rooms/1/chat', {}, JSON.stringify({
       text: '안녕하세요'
   }));
   ```

3. **메시지 브로커(Broker)**: "알림 배달부"
   - 누가 어떤 주소를 구독했는지 관리
   - 메시지를 적절한 구독자들에게 전달

### 주소 체계 (Destination Patterns)

```java
// WebSocketConfig.java에서 설정한 주소 체계

// 1. /app/* - 서버로 보내는 주소
registry.setApplicationDestinationPrefixes("/app");
// 클라이언트가 서버에게 메시지를 보낼 때 사용
// 예: /app/rooms/1/chat → 서버의 @MessageMapping("/rooms/1/chat") 메서드가 처리

// 2. /topic/* - 브로드캐스트 주소 (1:N)
registry.enableSimpleBroker("/topic", "/queue");
// 같은 토픽을 구독한 모든 사람에게 메시지 전송
// 예: /topic/rooms/1 → 1번 방의 모든 참가자가 받음

// 3. /queue/* - 개인 메시지 주소 (1:1)
// 특정 사용자 한 명에게만 메시지 전송

// 4. /user/* - 사용자별 개인 주소
registry.setUserDestinationPrefix("/user");
// 서버에서 특정 사용자에게 메시지를 보낼 때 사용
```

## 3. 실제 채팅 시스템 구현 분석

이 채팅 시스템은 **두 가지 통신 방식**을 함께 사용합니다:
1. **WebSocket (STOMP)** - 실시간 채팅 메시지 송수신
2. **HTTP REST API** - 과거 채팅 기록 조회

### 백엔드 아키텍처

```
WebSocketConfig     - WebSocket 및 STOMP 설정
ChatController      - STOMP 메시지 수신 (실시간)
ChatMessageController - HTTP REST API (과거 기록)
ChatSocketService   - 실시간 메시지 저장 + 브로드캐스트
ChatMessageService  - 과거 메시지 조회
```

### 프론트엔드 아키텍처

```javascript
// websocketService.js - 전역 WebSocket 상태 관리
let client = null;                    // STOMP 클라이언트 인스턴스
let state = ConnectionState.DISCONNECTED;  // 현재 연결 상태
const subscriptions = new Map();      // 구독 정보 저장소
```

## 4. 완전한 동작 시나리오 분석

### 시나리오 1: 사용자가 채팅방에 입장

#### 1단계: 애플리케이션 초기화
```javascript
// 프론트엔드 앱 시작
import { connect, setOnStateChange } from '@/services/websocketService';

// 1. 연결 상태 리스너 등록
setOnStateChange((newState) => {
    updateConnectionUI(newState); // UI 상태 배지 업데이트
});

// 2. WebSocket 연결 시작
connect();
```

**connect() 함수 동작**:
```javascript
export const connect = () => {
    const next = new Client({
        brokerURL: WS_URL,  // 'ws://localhost:9005/ws'
        onConnect: () => {
            setState(ConnectionState.CONNECTED);
            // 기존 구독들 자동 복구 (재연결 시)
            subscriptions.forEach((entry, destination) => {
                const sub = next.subscribe(destination, entry.handler);
                entry.subscription = sub;
            });
        },
        // 에러 시 자동 재연결
        onStompError: () => scheduleReconnect(),
        onWebSocketError: () => scheduleReconnect(),
    });
    
    setState(ConnectionState.CONNECTING);
    next.activate(); // 실제 WebSocket 연결
};
```

#### 2단계: 채팅방 입장 시 초기화
```javascript
// React 컴포넌트에서
useEffect(() => {
    // 1. 과거 메시지 불러오기 (HTTP)
    const loadPastMessages = async () => {
        const response = await fetch('/api/rooms/123/messages?size=20');
        const data = await response.json();
        setMessages(data.data); // 기존 20개 메시지 표시
    };
    
    // 2. 실시간 메시지 구독 (WebSocket)
    const unsubscribe = subscribe('/topic/rooms/123', (message) => {
        const chatMessage = JSON.parse(message.body);
        setMessages(prev => [...prev, chatMessage]);
    });
    
    loadPastMessages();
    return unsubscribe; // 언마운트 시 구독 해제
}, []);
```

**HTTP 과거 메시지 조회 흐름**:
```
React → fetch('/api/rooms/123/messages')
         ↓
ChatMessageController.getRecentMessages()
         ↓
ChatMessageService.getRecentMessages()
         ↓
ChatMessageRepository.findRecentMessagesByRoomId()
         ↓
DB 조회 → ChatMessage 엔티티들
         ↓
ChatMessageResponse.from() 변환
         ↓
JSON 응답 → React 상태 업데이트 → UI 렌더링
```

### 시나리오 2: 사용자가 메시지 전송

#### 1단계: 메시지 전송 (프론트엔드)
```javascript
const handleSendMessage = (content) => {
    const success = send('/app/rooms/123/chat', {
        content: content
    });
    
    if (!success) {
        alert('메시지 전송 실패');
    }
};
```

#### 2단계: STOMP 메시지 처리 (백엔드)
```java
// ChatController.java
@MessageMapping("/rooms/{roomId}/chat")
public void handleChat(
    @DestinationVariable Long roomId,    // 123
    @Payload ChatSendRequest request,    // { content: "안녕하세요" }
    Principal principal                   // 인증된 사용자
) {
    final String providerId = principal.getName(); // "user123"
    
    if (request.content().isBlank()) return;
    
    // 서비스에 위임
    chatSocketService.saveAndBroadcast(roomId, request, providerId);
}
```

#### 3단계: 메시지 저장 및 브로드캐스트
```java
// ChatSocketService.java
@Transactional
public void saveAndBroadcast(Long roomId, ChatSendRequest request, String providerId) {
    // 1. 보낸 사람 확인
    User sender = userRepository.findByProviderId(providerId).orElse(null);
    if (sender == null) return;

    // 2. 방 존재 확인
    StudyRoom room = studyRoomRepository.findById(roomId).orElse(null);
    if (room == null) return;

    // 3. 권한 검증
    boolean joined = roomParticipantRepository
        .findByRoomIdAndUserId(roomId, sender.getId()).isPresent();
    if (!joined) return;

    // 4. DB 저장
    ChatMessage saved = chatMessageRepository.save(ChatMessage.builder()
            .room(room)
            .sender(sender)
            .content(request.content())
            .build());

    // 5. 실시간 브로드캐스트
    ChatMessageResponse payload = ChatMessageResponse.from(saved);
    messagingTemplate.convertAndSend("/topic/rooms/" + roomId, payload);
}
```

#### 4단계: 실시간 메시지 수신 (모든 참여자)
```javascript
// 모든 방 참여자의 브라우저에서 실행
subscribe('/topic/rooms/123', (message) => {
    const chatMessage = JSON.parse(message.body);
    // {
    //   id: 456,
    //   roomId: 123,
    //   senderId: 789,
    //   senderNickname: "사용자A",
    //   content: "안녕하세요",
    //   createdAt: "2025-09-15T10:30:00"
    // }
    setMessages(prev => [...prev, chatMessage]); // UI 업데이트
});
```

## 5. 전체 메시지 흐름도

```
🔄 실시간 메시지 전송 흐름:
React 컴포넌트
    ↓ send('/app/rooms/123/chat')
websocketService.js
    ↓ STOMP publish
WebSocket 연결
    ↓
ChatController.handleChat()
    ↓
ChatSocketService.saveAndBroadcast()
    ↓ (병렬 처리)
┌─ DB 저장 ─┐    ┌─ 브로드캐스트 ─┐
│ Repository │    │ messagingTemplate │
└───────────┘    └─────────────────┘
                          ↓ '/topic/rooms/123'
                    WebSocket 연결들
                          ↓
                 모든 참여자 브라우저
                          ↓ subscribe 핸들러
                    React 상태 업데이트
                          ↓
                      UI 실시간 갱신

📄 과거 메시지 조회 흐름:
React 컴포넌트
    ↓ fetch('/api/rooms/123/messages')
ChatMessageController
    ↓
ChatMessageService
    ↓
ChatMessageRepository
    ↓
DB 조회 (최신 20개)
    ↓
ChatMessageResponse 변환
    ↓
JSON 응답
    ↓
React 상태 업데이트
    ↓
UI 렌더링
```

## 6. 자동 재연결 메커니즘

```javascript
// 네트워크 끊김 시 자동 복구
const scheduleReconnect = () => {
    if (!reconnectAllowed) return; // 수동 종료 시 재연결 안함
    
    retryCount += 1;
    // 지수 백오프: 0.5초 → 1초 → 2초 → 4초 → 8초 → 10초(최대)
    const delay = Math.min(baseDelayMs * 2 ** (retryCount - 1), maxDelayMs);
    
    setTimeout(() => {
        connect(); // 재연결 시도
    }, delay);
};

// 재연결 성공 시 구독 복구
onConnect: () => {
    subscriptions.forEach((entry, destination) => {
        const sub = client.subscribe(destination, entry.handler);
        entry.subscription = sub; // 새 구독 객체로 교체
    });
}
```

## 7. 언제 어떤 방식을 사용할까?

### HTTP가 적합한 경우:
- 단순한 데이터 조회/저장
- 파일 업로드/다운로드
- RESTful API
- 과거 채팅 기록 조회
- SEO가 중요한 웹페이지

### WebSocket이 적합한 경우:
- 실시간 채팅
- 온라인 게임
- 주식 가격 모니터링
- 협업 도구 (구글 독스 같은)
- 라이브 알림
- 실시간 메시지 송수신

## 8. 핵심 설계 원칙

1. **이중화된 통신 구조**:
   - HTTP: 안정적인 과거 데이터 + RESTful API
   - WebSocket: 실시간 양방향 통신

2. **관심사 분리**:
   - ChatController: STOMP 메시지 수신만
   - ChatMessageController: HTTP REST API만
   - ChatSocketService: 비즈니스 로직 (저장 + 브로드캐스트)

3. **자동 복구 시스템**:
   - 연결 끊김 시 지수 백오프로 재연결
   - 재연결 시 기존 구독 자동 복구
   - 수동 종료 vs 예기치 않은 끊김 구분

4. **상태 관리**:
   - 모듈 스코프 싱글톤으로 전역 WebSocket 상태 관리
   - React 컴포넌트는 구독/해제만 담당

5. **보안**:
   - 핸드셰이크 단계에서 JWT 인증
   - 메시지 전송 시 방 참여자 권한 확인