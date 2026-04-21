# AI·xAPI 기반 인재양성 사업 통합 운영 허브 플랫폼
# 개발 태스크 목록 명세서 (TASK LIST)

**기준 문서:** SRS-001 v1.0 (SRS_v1.0.md)  
**작성일:** 2026-04-21  
**작성 기준:** ISO/IEC/IEEE 29148:2018 / CQRS + TDD 패턴 오케스트레이션

---

## 처리 순서 원칙

| 단계 | 설명 |
|:---:|:---|
| **Step 1** | 계약·데이터 명세 (DB 스키마, API Contract, Mock) — SSOT 확립 |
| **Step 2** | 비즈니스 로직 (Query Read / Command Write) — CQRS 분리 |
| **Step 3** | 테스트 코드 (AC → 자동화 테스트로 변환) — 자동 피드백 루프 |
| **Step 4** | 비기능 요구사항 (인프라, 보안, 성능, 운영) — NFR Task |

---

## Step 1. 계약 및 데이터 명세 태스크

### 1-A. 데이터베이스 스키마 & 마이그레이션

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| DB-001 | 공통/인프라 | Prisma 스키마 초기화 — Organization, User 테이블 정의 및 마이그레이션 스크립트 작성 | §6.2.1 Organization, User Entity | None | M |
| DB-002 | F1 — 업로더 | Upload, MappingPattern 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 Upload, MappingPattern Entity | DB-001 | M |
| DB-003 | F2 — 데이터허브 | Dataset, CleansingLog 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 Dataset, CleansingLog Entity | DB-001 | M |
| DB-004 | F3 — 리포트 | Report 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 Report Entity | DB-003 | M |
| DB-005 | F4 — Badge | Badge, BadgeClass 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 Badge, BadgeClass Entity | DB-001 | M |
| DB-006 | F5 — 인재열람 | TalentProfile, ViewingCredit 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 TalentProfile, ViewingCredit Entity | DB-001, DB-005 | M |
| DB-007 | 공통/보안 | AuditLog 테이블 스키마 정의 및 마이그레이션 스크립트 작성 | §6.2.1 AuditLog Entity | DB-001 | L |
| DB-008 | 공통/인프라 | xAPI Statement 저장용 테이블(또는 Supabase 연계 LRS) 스키마 설계 | §3.1 EXT-02, REQ-FUNC-010 | DB-001 | H |
| DB-009 | 공통/인프라 | Supabase RLS(Row Level Security) 정책 정의 — 역할별(ADMIN, MANAGER, HR, LEARNER) 접근 정책 전체 적용 | §4.1.8 REQ-FUNC-038, REQ-FUNC-039 | DB-001 ~ DB-008 | H |
| DB-010 | 공통/인프라 | 로컬 개발용 SQLite(Prisma) Seed 데이터 스크립트 작성 (Organization 3건, User 5건, 역할별) | §6.2.1, C-TEC-003 | DB-001 ~ DB-008 | L |

### 1-B. API Contract & DTO 명세

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| API-001 | 공통/인증 | Supabase Auth 기반 로그인/세션 DTO 및 에러 코드 정의 (401, 403, 423 계정잠금) | §4.1.8 REQ-FUNC-038, §3.3 | DB-009 | M |
| API-002 | F1 — 업로더 | `uploadAndParseExcel` Server Action Request/Response DTO 정의 (JSON payload 구조, 처리 상태 ENUM) | §6.1 #1, §3.3, §6.2.1 Upload | DB-002 | M |
| API-003 | F1 — 업로더 | `confirmAiMapping` Server Action DTO 정의 (수동 보정 요청/응답) | §6.1 #3 | API-002 | L |
| API-004 | F2 — 데이터허브 | `mergeDatasets` Server Action DTO 정의 (병합 요청/결과, 오류 항목 목록) | §6.1 #4 | DB-003 | M |
| API-005 | F2 — 데이터허브 | `rollbackDataset` Server Action DTO 정의 (버전 롤백 요청/응답) | §6.1 #5 | API-004 | L |
| API-006 | F3 — 리포트 | `generateIntegrityReport` Server Action DTO 정의 (요청 파라미터, StorageURL 응답) | §6.1 #6, §3.6.2 | DB-004 | M |
| API-007 | F4 — Badge | `issueBadge` Server Action DTO 정의 (발급 요청, Assertion JSON 응답 구조) | §6.1 #7, §6.2.1 Badge | DB-005 | M |
| API-008 | F5 — 인재열람 | `searchTalents` Server Action DTO 정의 (필터 조건, 목록 응답 — 마스킹 처리 포함) | §6.1 #8, §3.6.3 | DB-006 | M |
| API-009 | F5 — 인재열람 | `viewTalentDetail` Server Action DTO 정의 (열람 요청, 상세 응답, 크레딧 차감 결과) | §6.1 #9 | API-008 | M |
| API-010 | F4 — Badge | `GET /api/v1/badges/verify/{badgeId}` Route Handler Response DTO 정의 (공개 검증 API) | §6.1 #10 | DB-005 | L |
| API-011 | F7 — LTI | `POST /api/lti/launch` Route Handler Request/Response DTO 정의 (Mock-up 구조) | §6.1 #11 | None | L |
| API-012 | xAPI/공통 | `POST /api/xapi/statements` Route Handler DTO 정의 (xAPI Statement 수신 및 저장 구조) | §6.1 #12, REQ-FUNC-010 | DB-008 | M |
| API-013 | F6 — 대시보드 | 대시보드 KPI 조회 Server Component Data Contract 정의 (집계 쿼리 결과 타입 인터페이스) | §3.3, §4.1.6 F6 | DB-001 ~ DB-007 | M |

### 1-C. Mock 데이터 & Fixture

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| MOCK-001 | F1 — 업로더 | 프론트엔드 개발용 비표준 엑셀 샘플 파일 5종 제작 (기관별 다른 컬럼 구조) | §4.1.1, §6.2.1 Upload | API-002 | L |
| MOCK-002 | F1 — 업로더 | `uploadAndParseExcel` 성공/실패(포맷 미지원, 용량 초과) Mocking 응답 데이터 작성 | §4.1.1 REQ-FUNC-005 | API-002 | L |
| MOCK-003 | F2 — 데이터허브 | 데이터 클렌징 시나리오용 오류 포함 Dataset Mock (결측값, 이상값, 수식 오류 각 10건) | §4.1.2 REQ-FUNC-008, REQ-FUNC-009 | API-004 | L |
| MOCK-004 | F4 — Badge | Local Fit Badge Assertion JSON 샘플 (IMS OB 2.0 표준 준수) | §4.1.4 REQ-FUNC-019, §6.2.1 Badge | API-007 | L |
| MOCK-005 | F5 — 인재열람 | 인재 프로필 목록 Mock 데이터 10건 (마스킹 버전/언마스킹 버전 분리) | §4.1.5 REQ-FUNC-024, REQ-FUNC-026 | API-008 | L |
| MOCK-006 | F3 — 리포트 | 무결성 리포트 검증 결과 JSON Mock (통과 케이스 / 실패 케이스) | §4.1.3 REQ-FUNC-018 | API-006 | L |

---

## Step 2. 로직 및 상태 변경 태스크 (CQRS)

### 2-A. 공통 기반 (Auth & Infra)

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| INFRA-001 | 공통/인프라 | Next.js 프로젝트 초기화 — App Router, Prisma, Supabase, Tailwind, shadcn/ui, Vercel AI SDK 전체 설정 | §1.2.3 C-TEC-001~008 | None | M |
| INFRA-002 | 공통/인프라 | Vercel 배포 파이프라인 구성 — Git Push 기반 CI/CD, 환경변수(.env) 관리 | §3.1 EXT-07, C-TEC-007 | INFRA-001 | M |
| INFRA-003 | 공통/인증 | Supabase Auth 통합 — 이메일 기반 로그인/로그아웃, 세션 미들웨어(middleware.ts) 적용 | §4.1.8 REQ-FUNC-038 | INFRA-001, DB-009, API-001 | H |
| INFRA-004 | 공통/인증 | 역할(RBAC) 기반 미들웨어 라우트 보호 구성 (ADMIN, MANAGER, HR, LEARNER 경로 분리) | §4.1.8 REQ-FUNC-038, REQ-FUNC-039 | INFRA-003 | M |
| INFRA-005 | 공통/인증 | 계정 잠금 정책 구현 — 연속 10회 실패 시 15분 잠금, failed_login_count 갱신 로직 | §4.1.8 REQ-FUNC-041, §4.2.3 REQ-NF-016 | INFRA-003, DB-001 | M |
| INFRA-006 | 공통/인프라 | 구조화 API 로그 미들웨어 구현 — 요청 ID(UUID), 타임스탬프, 엔드포인트, 응답 코드, 지연 시간 기록 | §4.1.8 REQ-FUNC-042 | INFRA-001 | M |
| INFRA-007 | 공통/인프라 | i18n(국제화) 구조 설정 — 한국어 기본 UI, 언어 리소스 키-값 파일 분리 | §4.1.8 REQ-FUNC-044 | INFRA-001 | L |
| INFRA-008 | 공통/인프라 | 알림(Notification) 공통 서비스 구현 — 인앱 알림 저장 및 이메일 발송(Supabase 이메일 템플릿) 연계 | §4.1.8 REQ-FUNC-043 | INFRA-003, DB-001 | M |

### 2-B. F1 — AI 스마트 엑셀·HWP 업로더

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F1-Q-001 | F1 — 업로더 | [Query] 업로드 처리 내역 목록 조회 Server Action (`getUploadHistory`) 구현 | §6.1 #2, REQ-FUNC-001 | INFRA-003, DB-002, API-002 | L |
| F1-Q-002 | F1 — 업로더 | [Query] 기관별 저장된 매핑 패턴 조회 로직 구현 (기존 MappingPattern 자동 적용 제안) | §4.1.1 REQ-FUNC-006 | DB-002 | L |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F1-C-001 | F1 — 업로더 | [Command/UI] 클라이언트 단 엑셀 파싱 컴포넌트 구현 (SheetJS, 파일 선택 → JSON 변환, 최대 10개 일괄) | §3.5, §3.6.1, REQ-FUNC-001, REQ-FUNC-004 | INFRA-001, MOCK-001 | M |
| F1-C-002 | F1 — 업로더 | [Command] `uploadAndParseExcel` Server Action 구현 — 세션 검증, 원본 파일 Supabase Storage 아카이빙, Upload 레코드 생성 | §6.1 #1, §6.3.1, REQ-FUNC-001 | INFRA-003, DB-002, API-002 | H |
| F1-C-003 | F1 — 업로더 | [Command] Vercel AI SDK + Gemini 연동 — 비표준 스키마 분석 및 표준 컬럼 매핑 프롬프트 호출 (`generateObject`) | §3.5, §4.1.1 REQ-FUNC-002, C-TEC-005, C-TEC-006 | F1-C-002, INFRA-001 | H |
| F1-C-004 | F1 — 업로더 | [Command] 파일 업로드 실패 처리 — 에러 코드(포맷 미지원, 손상, 용량 초과) DB 기록 및 재시도 로직 | §4.1.1 REQ-FUNC-005 | F1-C-002 | L |
| F1-C-005 | F1 — 업로더 | [Command] `confirmAiMapping` Server Action 구현 — 수동 보정 결과 DB 저장, MappingPattern 업서트 | §6.1 #3, REQ-FUNC-003, REQ-FUNC-006 | F1-C-003, DB-002 | M |
| F1-C-006 | F1 — 업로더 | [Command/UI] AI 매핑 결과 미리보기 및 수동 보정 편집 뷰 UI 구현 (원본-표준 컬럼 매핑 테이블 shadcn/ui) | §4.1.1 REQ-FUNC-003 | F1-C-003, MOCK-002 | M |
| F1-C-007 | F1 — 업로더 | [Command] 파일별 실시간 처리 상태 표시 UI 구현 (일괄 10개 처리 중 각 파일 개별 상태 피드백) | §4.1.1 REQ-FUNC-004 | F1-C-002 | M |

### 2-C. F2 — 통합 데이터 허브

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F2-Q-001 | F2 — 데이터허브 | [Query] 통합 데이터셋 기관별·지표별·기간별 필터링 조회 로직 구현 (Prisma 쿼리, p95 ≤ 1초) | §4.1.2 REQ-FUNC-011, REQ-NF-004 | DB-003, API-004 | M |
| F2-Q-002 | F2 — 데이터허브 | [Query] 데이터셋 버전 이력 목록 조회 로직 구현 | §4.1.2 REQ-FUNC-012 | DB-003 | L |
| F2-Q-003 | F2 — 데이터허브 | [Query/UI] 클렌징 오류 목록 조회 화면 구현 — 원본값/오류 유형/보정 제안값 표시 (shadcn/ui Table) | §4.1.2 REQ-FUNC-009 | DB-003, MOCK-003 | M |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F2-C-001 | F2 — 데이터허브 | [Command] `mergeDatasets` Server Action 구현 — Prisma Transaction 기반 멀티소스 병합, 중복 제거, Dataset 레코드 생성 | §6.1 #4, REQ-FUNC-007 | DB-003, API-004, F1-C-005 | H |
| F2-C-002 | F2 — 데이터허브 | [Command] 자동 데이터 클렌징 로직 구현 — 결측값 탐지, 이상값 플래깅, 수식 검증, CleansingLog 기록 | §4.1.2 REQ-FUNC-008 | F2-C-001, DB-003 | H |
| F2-C-003 | F2 — 데이터허브 | [Command] 클렌징 오류 보정 처리 — 적용(APPLIED)/무시(IGNORED) 선택 후 DB 상태 변경, xAPI Statement 기록 | §4.1.2 REQ-FUNC-009, REQ-FUNC-010 | F2-C-002, DB-008, API-012 | M |
| F2-C-004 | F2 — 데이터허브 | [Command] xAPI Statement 자동 기록 연동 — 데이터 정제·병합·보정 이벤트마다 Actor/Verb/Object/Timestamp 포함 Statement 생성 | §4.1.2 REQ-FUNC-010, §6.1 #12 | DB-008, API-012 | M |
| F2-C-005 | F2 — 데이터허브 | [Command] `rollbackDataset` Server Action 구현 — 특정 버전으로 Dataset 상태 복원, 롤백 이벤트 xAPI 기록 | §6.1 #5, REQ-FUNC-012 | F2-C-001, DB-003 | H |

### 2-D. F3 — 감사 대응 무결성 리포트

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F3-Q-001 | F3 — 리포트 | [Query] 생성된 리포트 목록 및 이력 조회 (생성자, 기간, 상태 포함) | §4.1.3 REQ-FUNC-016 | DB-004 | L |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F3-C-001 | F3 — 리포트 | [Command] 리포트 생성 전 자동 검증 로직 구현 — 수식 무결성 체크, 누락 필드 체크, 데이터 타입 일관성 체크 (실패 시 생성 중단) | §4.1.3 REQ-FUNC-018 | F2-C-002, DB-003, MOCK-006 | H |
| F3-C-002 | F3 — 리포트 | [Command] `generateIntegrityReport` Server Action 구현 — 데이터 로드, 검증 통과 후 Node.js 단 렌더링 실행 | §6.1 #6, §3.6.2, REQ-FUNC-013 | F3-C-001, DB-004, API-006 | H |
| F3-C-003 | F3 — 리포트 | [Command] PDF/Excel 리포트 파일 렌더링 및 Supabase Storage 적재 후 Signed URL 반환 | §4.1.3 REQ-FUNC-014, REQ-FUNC-015 | F3-C-002 | H |
| F3-C-004 | F3 — 리포트 | [Command] 리포트 생성 xAPI Statement 기록 — Actor(생성자), Verb(generated), Object(리포트ID), Context(데이터셋 버전) | §4.1.3 REQ-FUNC-016 | F3-C-002, DB-008 | L |
| F3-C-005 | F3 — 리포트 | [Command] 스케줄 기반 자동 리포트 생성 — Vercel Cron Job 설정, 완료 후 수신자 알림 발송(이메일/인앱) | §4.1.3 REQ-FUNC-017, INFRA-008 | F3-C-002, INFRA-008 | M |
| F3-C-006 | F3 — 리포트 | [Command/UI] 리포트 생성 요청 버튼 및 다운로드 링크 UI 구현 (shadcn/ui) | §3.6.2 | F3-C-003 | L |

### 2-E. F4 — Local Fit Open Badge 발급·관리

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F4-Q-001 | F4 — Badge | [Query] 학습자 Badge 보유 현황 및 발급 진행률 조회 (`getUserBadges`) | §6.3.2, REQ-FUNC-023 | DB-005, INFRA-003 | L |
| F4-Q-002 | F4 — Badge | [Query] Badge 공개 검증 페이지 — `GET /api/v1/badges/verify/{badgeId}` Route Handler 구현 (외부 제3자 공개) | §4.1.4 REQ-FUNC-021 | DB-005, API-010 | M |
| F4-Q-003 | F4 — Badge | [Query] Badge KPI 집계 조회 — 발급 건수, 기업별 열람 건수, 면접 전환율 (관리자용 대시보드 집계) | §4.1.4 REQ-FUNC-023 | DB-005, DB-006 | M |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F4-C-001 | F4 — Badge | [Command] BadgeClass 발급 규칙 엔진 설정 — 관리자가 발급 조건(criteria_rules JSON) 생성·수정 | §4.1.4 REQ-FUNC-020 | DB-005, INFRA-004 | M |
| F4-C-002 | F4 — Badge | [Command] xAPI 학습 이력 기반 Badge 자격 자동 판정 로직 구현 (`evaluateEligibility`) | §4.1.4 REQ-FUNC-022, §6.3.2 | DB-005, DB-008, F4-C-001 | H |
| F4-C-003 | F4 — Badge | [Command] `issueBadge` Server Action 구현 — IMS OB 2.0 Assertion JSON 생성, Badge 레코드 DB 저장 (외부 연동 없이 내부 저장) | §6.1 #7, §6.3.2, REQ-FUNC-019 | F4-C-002, DB-005, API-007 | H |
| F4-C-004 | F4 — Badge | [Command/UI] 학습자 Badge 프로필 관리 화면 구현 — 발급 현황, 진행률 표시, Badge 상세 (shadcn/ui Card) | §3.2 CLI-03, REQ-FUNC-019 | F4-C-003, MOCK-004 | M |

### 2-F. F5 — 기업용 인재 열람권 (Outbound Recruiting)

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F5-Q-001 | F5 — 인재열람 | [Query] `searchTalents` Server Action 구현 — Badge 필터, 직무 필터 조건 기반 인재 목록 조회(마스킹, RLS 적용), p95 ≤ 2초 | §6.1 #8, §6.3.3, REQ-FUNC-024, REQ-NF-004 | DB-006, DB-009, API-008, MOCK-005 | M |
| F5-Q-002 | F5 — 인재열람 | [Query/UI] 인재 검색 필터 UI 구현 — Badge 보유 여부, 직무, 지역 필터 적용 화면(shadcn/ui) | §4.1.5 REQ-FUNC-024 | F5-Q-001 | M |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F5-C-001 | F5 — 인재열람 | [Command] `viewTalentDetail` Server Action 구현 — Prisma Transaction으로 ViewingCredit 차감(-1), 크레딧 부족 시 오류 반환 | §6.1 #9, §6.3.3, REQ-FUNC-025 | DB-006, API-009 | H |
| F5-C-002 | F5 — 인재열람 | [Command] 인재 상세 프로필 열람 후 xAPI Statement 기록 — Actor(HR), Verb(viewed), Object(인재 프로필) | §4.1.5 REQ-FUNC-027 | F5-C-001, DB-008 | L |
| F5-C-003 | F5 — 인재열람 | [Command/UI] 인재 상세 프로필 카드 UI 구현 — Badge 목록, 12개월 학습 이력 요약, 지역 활동 이력 (shadcn/ui Card) | §4.1.5 REQ-FUNC-026 | F5-C-001, MOCK-005 | M |
| F5-C-004 | F5 — 인재열람 | [Command] 기업 관심 알림 발송 로직 구현 — 익명/실명 선택 기반 인앱 알림 및 이메일 발송, 이벤트 기록 | §4.1.5 REQ-FUNC-028 | INFRA-008, F5-C-001 | M |

### 2-G. F6 — 관리자 전용 ROI·성과 대시보드

#### [Query — 조회]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F6-Q-001 | F6 — 대시보드 | [Query] 데이터 취합 소요 시간 추이 집계 쿼리 구현 (AS-IS 48h vs TO-BE 월별 추이, p95 ≤ 3초) | §4.1.6 REQ-FUNC-029, REQ-NF-005 | DB-002, DB-003 | M |
| F6-Q-002 | F6 — 대시보드 | [Query] 보고서 에러 건수 KPI 집계 쿼리 구현 및 임계치(1건/월) 초과 알림 트리거 | §4.1.6 REQ-FUNC-030 | DB-004 | M |
| F6-Q-003 | F6 — 대시보드 | [Query] Badge 발급·열람·면접 전환율 KPI 집계 쿼리 구현 (채용 확정 건수 포함) | §4.1.6 REQ-FUNC-031 | DB-005, DB-006 | M |
| F6-Q-004 | F6 — 대시보드 | [Query] 기관별 데이터 품질 점수(결측값 비율, 이상값 비율, 표준화 준수율) 산출 집계 쿼리 구현 | §4.1.6 REQ-FUNC-032 | DB-003 | M |
| F6-Q-005 | F6 — 대시보드 | [Query/UI] ROI 대시보드 React Server Component 구현 — KPI 위젯 Prisma 직접 쿼리 SSR (shadcn/ui Chart) | §3.3, §4.1.6, API-013 | F6-Q-001 ~ F6-Q-004 | H |

#### [Command — 상태 변경]

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F6-C-001 | F6 — 대시보드 | [Command] 대시보드 위젯 레이아웃 Drag & Drop 커스터마이징 저장 로직 구현 (Zustand 상태 + DB 저장) | §4.1.6 REQ-FUNC-033 | F6-Q-005 | M |
| F6-C-002 | F6 — 대시보드 | [Command] 대시보드 데이터 CSV/PDF 내보내기 Server Action 구현 (5초 이내 생성) | §4.1.6 REQ-FUNC-034 | F6-Q-005 | M |

### 2-H. F7 — LTI 표준 기반 LMS 플러그인 연동 (Mock-up)

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| F7-C-001 | F7 — LTI연동 | [Command] `POST /api/lti/launch` Mock-up Route Handler 구현 — 서명 무시, 모의 토큰 발급, 테스트 화면 렌더링 | §4.1.7 REQ-FUNC-035, 미결이슈 #2 | INFRA-001, API-011 | M |
| F7-C-002 | F7 — LTI연동 | [Command] LTI 사용자 컨텍스트 파싱 로직 구현 — 기관/과목/역할 파싱 후 데이터 범위 제한 (Phase 1 Mock-up 수준) | §4.1.7 REQ-FUNC-036 | F7-C-001 | M |
| F7-C-003 | F7 — LTI연동 | [Command] LMS 학습 활동 이벤트 xAPI Statement 변환 및 LRS 저장 로직 구현 | §4.1.7 REQ-FUNC-037 | F7-C-001, DB-008 | M |

### 2-I. 공통 — 감사 로그 & 멀티테넌트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| CMN-C-001 | 공통/보안 | [Command] 사용자 활동 감사 로그 자동 기록 미들웨어 구현 — 수정자, 수정 시각, 수정 전/후 값, IP 기록 | §4.1.8 REQ-FUNC-040, §6.2.1 AuditLog | DB-007, INFRA-003 | M |
| CMN-C-002 | 공통/보안 | [Command] 멀티테넌트 데이터 분리 검증 — Supabase RLS 정책 기반 기관 간 데이터 교차 접근 원천 차단 | §4.1.8 REQ-FUNC-039 | DB-009, INFRA-003 | H |
| CMN-C-003 | 공통/보안 | [Command] 알림 선호도 설정 Server Action 구현 — 이메일/인앱 선호도 User.notification_pref 저장 | §4.1.8 REQ-FUNC-043 | INFRA-003, API-001 | L |

---

## Step 3. 테스트 태스크 (AC → 자동화 테스트 변환)

### 3-A. F1 — 업로더 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F1-001 | F1 — 업로더 | [Test/Unit] 파일 포맷 식별 단위 테스트 — xlsx/xls/hwp/csv 진입 성공 + 미지원 포맷 400 에러 반환 시나리오 (TC-001) | §5.1 TC-001 / REQ-FUNC-001 | F1-C-002 | L |
| TEST-F1-002 | F1 — 업로더 | [Test/Unit] AI 매핑 응답 및 수동 보정 단위 테스트 — 비표준 컬럼 매핑 제안 생성 GWT + 직접 수정 반영 (TC-002, TC-003) | §5.1 TC-002, TC-003 / REQ-FUNC-002, 003 | F1-C-003, F1-C-006 | M |
| TEST-F1-003 | F1 — 업로더 | [Test/Perf] 10개 파일 일괄 처리 성능 테스트 — p95 ≤ 5초/파일(10MB 기준) 검증 (TC-004, TC-NF-002) | §5.1 TC-004 / REQ-NF-002 | F1-C-001, F1-C-002 | M |
| TEST-F1-004 | F1 — 업로더 | [Test/Unit] 업로드 실패 에러 처리 단위 테스트 — 손상 파일/용량 초과(>50MB)/미지원 포맷 각각 에러 코드/재시도 검증 (TC-005, TC-NF-007) | §5.1 TC-005 / REQ-FUNC-005, REQ-NF-007 | F1-C-004 | L |
| TEST-F1-005 | F1 — 업로더 | [Test/Unit] 기관별 매핑 패턴 자동 적용 단위 테스트 — 이전 패턴 존재 시 자동 적용 알림, 미존재 시 신규 흐름 GWT (TC-006) | §5.1 TC-006 / REQ-FUNC-006 | F1-Q-002, F1-C-005 | L |

### 3-B. F2 — 데이터허브 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F2-001 | F2 — 데이터허브 | [Test/Integration] 멀티소스 데이터 병합 통합 테스트 — 5개 기관 데이터 병합 후 건수/중복 제거 수 검증 (TC-007) | §5.1 TC-007 / REQ-FUNC-007 | F2-C-001, MOCK-003 | M |
| TEST-F2-002 | F2 — 데이터허브 | [Test/Unit] 자동 클렌징 단위 테스트 — 결측값·이상값·수식 오류 탐지 및 플래그 부여 GWT 3케이스 (TC-008, TC-009) | §5.1 TC-008, TC-009 / REQ-FUNC-008, REQ-FUNC-009 | F2-C-002, F2-C-003 | M |
| TEST-F2-003 | F2 — 데이터허브 | [Test/Security] xAPI 감사 추적 기록 테스트 — 정제 이벤트 후 Actor/Verb/Object/Timestamp 필드 존재 검증 (TC-010) | §5.1 TC-010 / REQ-FUNC-010 | F2-C-004 | L |
| TEST-F2-004 | F2 — 데이터허브 | [Test/Perf] 데이터 필터링 응답 시간 테스트 — 기관/지표/기간 복합 필터 p95 ≤ 1초 검증 (TC-011) | §5.1 TC-011 / REQ-FUNC-011 | F2-Q-001 | L |
| TEST-F2-005 | F2 — 데이터허브 | [Test/Unit] 데이터 롤백 단위 테스트 — 3회 수정 후 2회차 시점 복원 GWT + 롤백 xAPI 기록 확인 (TC-012) | §5.1 TC-012 / REQ-FUNC-012 | F2-C-005 | M |

### 3-C. F3 — 리포트 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F3-001 | F3 — 리포트 | [Test/Perf] 무결성 리포트 생성 성능 테스트 — p95 ≤ 3분 내 생성 완료 검증 (TC-013, TC-NF-003) | §5.1 TC-013 / REQ-NF-003 | F3-C-002 | M |
| TEST-F3-002 | F3 — 리포트 | [Test/Validation] 리포트 내용 완전성 검증 테스트 — 4개 섹션(출처, 이력, 비교, 검증) 모두 포함 여부 (TC-014) | §5.1 TC-014 / REQ-FUNC-014 | F3-C-003 | L |
| TEST-F3-003 | F3 — 리포트 | [Test/Unit] 자동 검증 로직 단위 테스트 — 수식 오류 1건 포함 데이터로 생성 시 리포트 생성 중단 + 에러 리포트 반환 GWT (TC-018) | §5.1 TC-018 / REQ-FUNC-018 | F3-C-001 | M |
| TEST-F3-004 | F3 — 리포트 | [Test/Unit] PDF/Excel 내보내기 단위 테스트 — 30초 이내 파일 생성 및 다운로드 링크 반환 검증 (TC-015) | §5.1 TC-015 / REQ-FUNC-015 | F3-C-003 | L |

### 3-D. F4 — Badge 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F4-001 | F4 — Badge | [Test/Standard] OB 2.0 Assertion JSON 포맷 유효성 단위 테스트 — IMS OB 2.0 스펙 필수 필드 존재 + DB 내부 저장 확인 (TC-019) | §5.1 TC-019 / REQ-FUNC-019 | F4-C-003, MOCK-004 | M |
| TEST-F4-002 | F4 — Badge | [Test/Unit] Badge 자격 판정 단위 테스트 — 조건 미충족(부정) / 충족(발급) 각 GWT 시나리오 (TC-022) | §5.1 TC-022 / REQ-FUNC-022 | F4-C-002 | M |
| TEST-F4-003 | F4 — Badge | [Test/Integration] Badge 공개 검증 URL 통합 테스트 — 외부 접근 시 발급 기관/일/기준/유효 여부 응답 검증 (TC-021) | §5.1 TC-021 / REQ-FUNC-021 | F4-Q-002 | L |

### 3-E. F5 — 인재열람 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F5-001 | F5 — 인재열람 | [Test/Perf] 인재 검색 응답 시간 테스트 — 복합 필터 적용 p95 ≤ 2초 검증 (TC-024, TC-NF-004) | §5.1 TC-024 / REQ-NF-004 | F5-Q-001 | M |
| TEST-F5-002 | F5 — 인재열람 | [Test/Unit] 열람권 차감 단위 테스트 — 크레딧 1건 차감 정상 / 잔액 0 시 오류 반환 GWT (TC-025) | §5.1 TC-025 / REQ-FUNC-025 | F5-C-001 | M |
| TEST-F5-003 | F5 — 인재열람 | [Test/Security] RLS 기반 인재 프로필 접근 통제 테스트 — MANAGER 역할로 인재 열람 API 우회 시도 시 DB 레벨 차단 확인 (TC-038) | §5.1 TC-038 / REQ-FUNC-038 | CMN-C-002, DB-009 | M |

### 3-F. F6 — 대시보드 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-F6-001 | F6 — 대시보드 | [Test/Perf] 대시보드 초기 로딩 성능 테스트 — SSR 기준 p95 ≤ 3초 검증 (TC-NF-005) | §5.1 TC-NF-005 / REQ-NF-005 | F6-Q-005 | L |
| TEST-F6-002 | F6 — 대시보드 | [Test/Unit] 에러 건수 KPI 알림 단위 테스트 — 월간 에러 1건 발생 시 관리자 알림 즉시 트리거 GWT (TC-030) | §5.1 TC-030 / REQ-FUNC-030 | F6-Q-002, INFRA-008 | L |

### 3-G. 공통 보안/인증 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| TEST-CMN-001 | 공통/보안 | [Test/Security] 계정 잠금 정책 테스트 — 10회 연속 로그인 실패 → 계정 15분 잠금 GWT (TC-NF-016) | §5.1 TC-NF-016 / REQ-NF-016 | INFRA-005 | L |
| TEST-CMN-002 | 공통/보안 | [Test/Security] 멀티테넌트 데이터 격리 테스트 — 기관 A 사용자가 기관 B 데이터 조회 시도 → 결과 0건 반환 (TC-039) | §5.1 TC-039 / REQ-FUNC-039 | CMN-C-002, DB-009 | M |
| TEST-CMN-003 | 공통/보안 | [Test/Security] 감사 로그 기록 완전성 테스트 — 데이터 수정 후 수정자/시각/전후값/IP 4필드 기록 확인 (TC-040) | §5.1 TC-040 / REQ-FUNC-040 | CMN-C-001 | L |
| TEST-CMN-004 | 공통/보안 | [Test/Security] bcrypt 비밀번호 저장 단위 테스트 — 비밀번호 설정 후 DB 조회 시 평문 미노출 + cost factor ≥ 12 확인 (TC-041) | §5.1 TC-041 / REQ-FUNC-041 | INFRA-003 | L |
| TEST-CMN-005 | 공통/운영 | [Test/Unit] API 구조화 로그 단위 테스트 — 임의 요청 후 UUID/ISO8601/엔드포인트/응답코드/latency JSON 구조 확인 (TC-042) | §5.1 TC-042 / REQ-FUNC-042 | INFRA-006 | L |

---

## Step 4. 비기능 요구사항 (NFR) 태스크

### 4-A. 성능 및 부하 테스트

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| NFR-P-001 | 성능 | [Perf] k6 부하 테스트 스크립트 작성 — 100 동시 요청 처리 검증 (TC-NF-006) / AI 매핑 p95 ≤ 5초 / 인재 검색 p95 ≤ 2초 / 대시보드 p95 ≤ 3초 | §4.2.1 REQ-NF-002, REQ-NF-004, REQ-NF-005, REQ-NF-006 | 전체 Command Task | H |
| NFR-P-002 | 성능 | [Perf] 대량 파싱 Chunking UI 구현 — 수만 Row 엑셀 파일을 청크 분할하여 여러 번 Server Action 호출하는 구조 | §7 미결이슈 #1, REQ-NF-007 | F1-C-001, F1-C-002 | H |
| NFR-P-003 | 비용/운영 | [Cost] AI 포맷 매핑 단위 처리 비용 추적 모니터링 구현 — Gemini API 호출 건수/비용 Vercel 로그 연계 대시보드 | §4.2.4 REQ-NF-017 | F1-C-003, INFRA-006 | M |

### 4-B. 가용성 및 재해복구

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| NFR-A-001 | 가용성 | [Availability] Supabase 자동 백업 설정 확인 및 RPO ≤ 1시간 달성 정책 문서화 | §4.2.2 REQ-NF-009 | INFRA-002 | L |
| NFR-A-002 | 가용성 | [DR] RTO ≤ 4시간 재해복구 절차 Runbook 문서화 (Vercel/Supabase 복구 시나리오) | §4.2.2 REQ-NF-010 | INFRA-002 | L |
| NFR-A-003 | 가용성 | [Monitoring] Vercel Analytics + Supabase Monitoring 기반 99.5% SLA 가동률 모니터링 대시보드 구성 | §4.2.2 REQ-NF-008 | INFRA-002 | M |

### 4-C. 보안

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| NFR-S-001 | 보안 | [Sec] TLS 1.2+ 적용 확인 — Vercel 기본 HTTPS 적용 및 커스텀 도메인 인증서 구성 검증 | §4.2.3 REQ-NF-012 | INFRA-002 | L |
| NFR-S-002 | 보안 | [Sec] Supabase AES-256 저장 암호화 적용 확인 — 개인정보 필드(email, 활동 이력) 암호화 at rest 검증 | §4.2.3 REQ-NF-014 | DB-001 | L |
| NFR-S-003 | 보안 | [Sec] 감사 로그 3년 보존 정책 구성 — Supabase WORM 또는 해시 체인 기반 변조 방지 메커니즘 구현 | §4.2.3 REQ-NF-015 | DB-007, CMN-C-001 | H |
| NFR-S-004 | 보안 | [Sec] 로그 마스킹 파이프라인 구축 — API 로그, 감사 로그에서 개인정보(이메일, 전화번호) 마스킹 처리 | §4.1.8 REQ-FUNC-041, §4.2.3 | INFRA-006, CMN-C-001 | M |

### 4-D. 운영 및 모니터링

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| NFR-O-001 | 운영 | [Monitoring] 실시간 시스템 지표 모니터링 구성 — CPU/메모리/API 응답 시간/에러율 ≤ 60초 주기 수집 (Vercel 로그 + Supabase Metrics) | §4.2.5 REQ-NF-018 | INFRA-002 | M |
| NFR-O-002 | 운영 | [Alerting] 에러율 5% 초과 시 5분 이내 자동 알림 구성 — Vercel 알림 훅 또는 외부 Slack 웹훅 연동 | §4.2.5 REQ-NF-019 | NFR-O-001 | M |
| NFR-O-003 | 운영 | [Ops] 유지보수 다운타임 공지 프로세스 정의 — 72시간 전 이메일/인앱 공지 템플릿 및 절차 문서화 | §4.2.2 REQ-NF-011 | INFRA-008 | L |

### 4-E. 확장성 및 아키텍처 검증

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|:---|:---|:---|:---|:---|:---:|
| NFR-E-001 | 확장성 | [Architecture] Next.js 단일 풀스택 아키텍처 검증 — 프론트/백엔드 통합 배포 모노레포 구조, 비즈니스 로직 `actions/` 디렉터리 격리 확인 | §4.2.6 REQ-NF-021, C-TEC-001, C-TEC-002 | INFRA-001 | L |
| NFR-E-002 | 확장성 | [Architecture] Phase 1 → Phase 2 (20 → 50 기관) 수평 확장 대응 구조 검증 — Supabase Connection Pool, Vercel 서버리스 스케일링 검증 계획 수립 | §4.2.6 REQ-NF-020 | DB-009, INFRA-002 | M |

---

## 전체 태스크 의존성 요약 (Dependency Map)

```
[DB-001~010, API-001~013, MOCK-001~006] (Step 1: SSOT 확립)
        ↓
[INFRA-001~008] (공통 기반)
        ↓
[F1-Q/C, F2-Q/C, F3-Q/C, F4-Q/C, F5-Q/C, F6-Q/C, F7-C, CMN-C] (Step 2: 기능 구현)
        ↓
[TEST-F1~F7, TEST-CMN] (Step 3: 자동화 테스트)
        ↓
[NFR-P, NFR-A, NFR-S, NFR-O, NFR-E] (Step 4: 비기능 검증)
```

### 핵심 블로킹 관계 (Critical Path)

| 선행 태스크 (Blocks) | 후속 태스크 (Depends on) | 이유 |
|:---|:---|:---|
| DB-001 | 모든 DB-002~010 | Organization, User SSOT 없이 나머지 스키마 정의 불가 |
| DB-009 (RLS) | INFRA-003, CMN-C-002 | RLS 없이 멀티테넌트 인증 구현 불가 |
| INFRA-001 | 모든 Feature Task | 프레임워크 초기화 없이 개발 착수 불가 |
| INFRA-003 | F1-C-002, F4-C-003, F5-C-001 | 세션 없이 Server Action 권한 제어 불가 |
| F1-C-005 | F2-C-001 | 매핑 패턴 확정 없이 병합 불가 |
| F2-C-002 | F3-C-001 | 클렌징 없이 리포트 검증 불가 |
| F4-C-002 | F4-C-003 | 자격 판정 없이 Badge 발급 불가 |
| DB-008 | F2-C-004, CMN-C-001 | xAPI LRS 스키마 없이 Statement 기록 불가 |

---

## 태스크 통계 요약

| 분류 | 태스크 수 |
|:---|:---:|
| Step 1 — DB 스키마 | 10 |
| Step 1 — API Contract | 13 |
| Step 1 — Mock 데이터 | 6 |
| Step 2 — 공통 인프라/인증 | 8 |
| Step 2 — F1 업로더 | 9 |
| Step 2 — F2 데이터허브 | 8 |
| Step 2 — F3 리포트 | 7 |
| Step 2 — F4 Badge | 7 |
| Step 2 — F5 인재열람 | 6 |
| Step 2 — F6 대시보드 | 7 |
| Step 2 — F7 LTI | 3 |
| Step 2 — 공통 감사/멀티테넌트 | 3 |
| Step 3 — F1~F7 테스트 | 18 |
| Step 3 — 공통 보안 테스트 | 5 |
| Step 4 — 성능/부하 | 3 |
| Step 4 — 가용성/DR | 3 |
| Step 4 — 보안 | 4 |
| Step 4 — 운영/모니터링 | 3 |
| Step 4 — 확장성 | 2 |
| **합계** | **125** |

---

*— 문서 종료 — TASK LIST v1.0*  
*기준 SRS: SRS-001 v1.0 (2026-04-21)*  
*작성 기준: ISO/IEC/IEEE 29148:2018 / CQRS + TDD 오케스트레이션*
