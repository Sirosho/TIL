# 📌 JPA의 Flush와 트랜잭션

## 1. 트랜잭션 생명주기

1. `@Transactional` 시작 → DB connection 획득 & autoCommit 꺼짐
2. 엔티티 변경 → **영속성 컨텍스트(1차 캐시)** 에만 저장
3. `flush` 발생 → SQL을 DB에 전송 (transaction log 기록)
4. `commit` → DB에 최종 확정
5. `rollback` → flush로 보낸 변경도 취소

---

## 2. flush란?

* **의미**: 영속성 컨텍스트의 변경 내용을 **DB로 즉시 반영**

* **git 으로 비유**:

  * `flush = git add` (변경사항을 DB에 스테이징)
  * `commit = git commit` (변경 확정)
  * `rollback = git reset` (취소)
* **특징**

  * commit 전에도 SQL 실행됨
  * rollback 시 취소 가능
  * 주로 **DB 제약 확인** 또는 **중간 상태 확정**에 사용

---

## 3. 테스트에서 flush 활용

* 테스트 메서드에 `@Transactional` 있으면 기본적으로 **종료 시 rollback**
* `saveAndFlush` 사용 → 테스트 도중 DB 반영 여부 확인 가능
* 테스트 끝나면 rollback → DB 깨끗하게 유지
* 장점:

  * 검증 가능 + 데이터 자동 정리
  * 테스트 간 독립성 보장

---

## ✅ 한 줄 정리

- flush는 변경사항을 DB에 미리 밀어넣는 것 
- commit은 그걸 확정하는 것. 
- rollback 시 flush도 무효화된다.

