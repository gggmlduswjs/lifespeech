# LifeSpeech API 명세서

#spec #api #lifespeech

**작성일:** 2026-02-11  
**버전:** 1.0

---

## 1. 외부 연동 API

### 1.1 네이버 예약

| 항목 | 내용 |
|------|------|
| 연동 목적 | 네이버 예약 접수 데이터를 시스템에 수집 |
| **공식 API** | ❌ **미제공** — booking.naver.com 전용 공개 API 없음 |
| **1차 대안** | 수동 입력 또는 CSV 업로드 (매장관리에서 내보내기) |
| 수집 데이터 | 예약자 이름, 연락처, 예약 일시, 메모 |
| 추가 확인 | 파트너사 문의 시 API 제공 여부 |

*상세: [[11-외부연동-참고자료#1. 네이버 예약]]*

---

### 1.2 전자계약 (모두싸인)

| 항목 | 내용 |
|------|------|
| 문서 | https://developers.modusign.co.kr/ |
| 인증 | API KEY (설정 → API → API KEY) |
| 연동 | 템플릿 기반 서명 요청, 전화번호로 알림 |
| Webhook | 서명 완료 등 이벤트 수신 (HTTPS 필수) |

*상세: [[11-외부연동-참고자료#2. 모두싸인]]*

---

### 1.3 문자 발송 (알리고)

| 항목 | 내용 |
|------|------|
| 사이트 | https://smartsms.aligo.in |
| API | POST https://apis.aligo.in/send/ |
| 필수 | key, user_id, sender, receiver, msg |
| 테스트 | testmode_yn=Y (실제 발송 제외) |

*상세: [[11-외부연동-참고자료#3. 알리고]]*

---

### 1.4 Google Calendar

| 항목 | 내용 |
|------|------|
| 용도 | 강사 가능 시간 동기화, 1:1 일정 매칭 |
| 범위 | 강사별 캘린더 읽기/쓰기 |

---

## 2. 내부 API (REST)

### 2.1 인증

| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | /auth/login | 로그인 (email/phone + password) |
| POST | /auth/logout | 로그아웃 |
| GET | /auth/me | 현재 사용자 정보, 역할 |
| POST | /auth/refresh | 토큰 갱신 |

---

### 공통: 목록 API 페이지네이션 (1,000명+ 대비)

| 파라미터 | 타입 | 기본값 | 설명 |
|----------|------|--------|------|
| page | int | 1 | 페이지 번호 |
| limit | int | 20 | 건수 (최대 100) |
| sort | string | created_at | 정렬 기준 |
| order | asc/desc | desc | 정렬 방향 |

*응답: `{ data, total, page, limit }` 형태*

---

### 2.2 예약

| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | /reservations | 예약 목록 (필터: 날짜, 상태) **+ 페이지네이션** |
| GET | /reservations/:id | 예약 상세 |
| POST | /reservations | 수동 등록 |
| PATCH | /reservations/:id/visit | 방문 체크 |
| PATCH | /reservations/:id/status | 상태 변경 (대기/미등록/등록) |

---

### 2.3 회원

| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | /members | 회원 목록 (필터: 과정, 반, 상태) **+ 페이지네이션 필수** |
| GET | /members/:id | 회원 상세 |
| POST | /members | 회원 등록 |
| PATCH | /members/:id | 회원 수정 |
| PATCH | /members/:id/status | 등록 상태 변경 |
| GET | /members/:id/history | 상담·출석 이력 |

---

### 2.4 출석

| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | /attendance | 출석부 (반/회차/날짜 필터) **+ 페이지네이션** |
| GET | /attendance/:id | 출석 상세 |
| PATCH | /attendance/:id | 출석 상태 변경 |
| POST | /attendance/bulk-register | 그룹반 7회차 일괄 등록 |
| POST | /attendance/close-session | 회차 마감 (재등록/종료 이동) |

---

### 2.5 스케줄

| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | /schedule | 주간 타임테이블 |
| GET | /schedule/instructor/:id | 강사별 스케줄 |
| POST | /schedule/1to1 | 1:1 일정 등록 |

---

### 2.6 파일

| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | /files/upload | 숙제·영상·문서 업로드 |
| GET | /files | 회원별 파일 목록 |
| GET | /files/:id | 파일 다운로드 |
| DELETE | /files/:id | 파일 삭제 |

---

### 2.7 문자

| Method | Endpoint | 설명 |
|--------|----------|------|
| POST | /sms/send | 문자 발송 (단건/ bulk) |
| GET | /sms/templates | 템플릿 목록 |

---

### 2.8 강사 체크리스트

| Method | Endpoint | 설명 |
|--------|----------|------|
| GET | /checklists | 회원별 체크리스트 |
| PATCH | /checklists/:id | 체크리스트 입력 |
| GET | /evaluations/:memberId | 회원 최종 평가 |

---

*[[07-DB-설계]] 참고. 인증: Bearer Token (JWT)*
