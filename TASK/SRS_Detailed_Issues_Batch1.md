# 개별 개발 태스크 명세서 (GitHub Issue 형상)

추출된 태스크 중 개발 병목이 높거나 시스템 기반이 되는 중요 태스크 5개에 대하여 `GitHub Issue Template` 형식을 엄격하게 준수하여 상세 내용을 작성하였습니다.

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-001: User, Organization, Auth, AuditLog 등 공통 기초 테이블 스키마 작성 및 Prisma 세팅"
labels: 'feature, backend, priority:high, database'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-001] 핵심 도메인 데이터 모델링 및 Prisma 초기화
- 목적: 시스템의 기반이 되는 사용자, 기관, 기본 인증 및 감사 로그(AuditLog) 테이블을 구축하여 이후 도메인 기능의 기반을 마련한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/docs/SRS_v1.0.md#621-core-entities`](#)
- 데이터 모델 (ERD): [`/docs/SRS_v1.0.md#622-entity-relationship-overview`](#)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Next.js 프로젝트 내 Prisma 초기화 (`npx prisma init`)
- [ ] `schema.prisma` 파일에 Organization, User, ViewingCredit, AuditLog 모델 작성
- [ ] 각 모델 간의 관계(Relations) 설정 (1:N, N:M 등)
- [ ] Supabase 연결 문자열(`DATABASE_URL`, `DIRECT_URL`) 환경 변수 구성
- [ ] 초기 데이터베이스 마이그레이션 파일 생성 및 적용 (`npx prisma migrate dev`)

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: Prisma 스키마 검증
- Given: `schema.prisma` 파일이 작성됨
- When: `npx prisma validate` 및 `npx prisma format`을 실행함
- Then: 에러 없이 정상적으로 검증을 통과한다.

Scenario 2: 데이터베이스 마이그레이션 적용
- Given: Supabase DB가 연결 가능한 상태
- When: `npx prisma migrate dev --name init_core_entities`를 실행함
- Then: 데이터베이스에 Organization, User, ViewingCredit, AuditLog 테이블이 ERD 요구사항 대로 성공적으로 생성된다.

## :gear: Technical & Non-Functional Constraints
- 안정성: Supabase 100% 의존에 따른 RLS(Row Level Security) 설정 전제로 Foreign Key 제약 조건 강제 적용.
- 보안: 비밀번호 자체 해시는 배제하되 Auth 연동에 필요한 `auth.users`와의 매핑 필드(UUID) 확보 처리.
- 제약사항: 멀티테넌트 구조 지원을 위해 관련 테이블에 `org_id` 외래키를 필수 연결 설계.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 데이터베이스 마이그레이션이 정상 배포(적용) 완료되었는가?
- [ ] Prisma Client 생성이 성공적으로 이루어졌는가 (`npx prisma generate`)?
- [ ] 모델별 필드명과 타입이 SRS 엔티티 정의서를 100% 일치하게 준수하는가?

## :construction: Dependencies & Blockers
- Depends on: 프로젝트 스캐폴딩 뼈대 구축
- Blocks: #DB-002, #DB-004, #INF-001

---

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] DB-002: Upload, MappingPattern, Dataset, CleansingLog 데이터 수집 관련 테이블 스키마 작성"
labels: 'feature, backend, priority:high, database'
assignees: ''
---

## :dart: Summary
- 기능명: [DB-002] 데이터 허브 및 클렌징 관리를 위한 테이블 스키마 구축
- 목적: 위탁 기관으로부터 수집되는 원천 데이터의 상태 및 클렌징 내역을 관리할 데이터 파이프라인 기반 모델을 생성한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/docs/SRS_v1.0.md#621-core-entities`](#)
- 데이터 모델 (ERD): [`/docs/SRS_v1.0.md#622-entity-relationship-overview`](#)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `schema.prisma`에 Upload, MappingPattern, Dataset, CleansingLog 모델 추가
- [ ] Upload 및 MappingPattern의 기관(Organization) 조인 구조 정립
- [ ] CleansingLog에 Action/Status Enum (APPLIED, IGNORED, PENDING 등) 설정
- [ ] Dataset 이력 관리 및 원본 파일 매핑을 위한 JSON 타입 필드(JSONB) 구조 설계
- [ ] 마이그레이션 적용 및 Prisma Client 갱신

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 마이그레이션 확정 및 DB 적용
- Given: 기존 공통 테이블(Core Entities)이 존재하는 환경
- When: 데이터 수집 파이프라인에 관련된 신규 모델을 prisma 파일에 적용하고 마이그레이션함
- Then: 기존 데이터를 유지하면서 Upload, MappingPattern 등의 신규 테이블 생성이 성공한다.

Scenario 2: JSONB 필드 제약 검증
- Given: 데이터베이스가 정상 생성됨
- When: Dataset의 `source_uploads` 필드에 JSON 데이터 Insert를 시도함
- Then: PostgreSQL의 JSONB/JSON 타입 제약에 맞게 구조 파싱되어 정상 저장된다.

## :gear: Technical & Non-Functional Constraints
- 확장성: Upload 및 Dataset은 텍스트 포맷 한계를 고려하여 JSONB 타입과 TEXT 타입을 적절히 활용한다.
- 데이터 정합성: CleansingLog는 Dataset에 강한 종속성(Cascading) 규칙을 설정하거나 논리적 보존 처리를 구성해야 한다.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] DB-001에서 만든 User/Organization과의 FK 정합성이 누락 없이 연결되었는가?
- [ ] Prisma Client 최신화 빌드 시 경고나 에러가 없는가?

## :construction: Dependencies & Blockers
- Depends on: #DB-001 (기본 테이블 생성완료)
- Blocks: #API-001, #DB-003

---

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] API-001: 엑셀 업로드, AI 매핑 승인, 병합, 데이터 롤백 Server Action Request/Response DTO 정의"
labels: 'feature, backend, priority:high, api'
assignees: ''
---

## :dart: Summary
- 기능명: [API-001] 데이터 허브 영역 Server Action DTO 및 통신 계약 명세 정의
- 목적: 클라이언트 뷰와 백엔드 로직 간의 강력한 타입 안정성을 보장하기 위해 Server Action 계층의 입출력 데이터 규격(Contract)을 수립한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/docs/SRS_v1.0.md#61-server-actions--route-handlers-master-list`](#)
- 파이프라인 API 흐름도: [`/docs/SRS_v1.0.md#361-핵심-시퀀스-1-엑셀-클라이언트-파싱-및-ai-기반-매핑-f1--f2`](#)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `actions/upload.ts`에 대한 `uploadAndParseExcel`, `confirmAiMapping` Request/Response Type(Zod Schema 등) 정의
- [ ] `actions/dataset.ts`에 대한 `mergeDatasets`, `rollbackDataset` 파라미터 및 반환 타입 정의
- [ ] API 표준 에러 응답 및 공통 반환 래퍼(Wrapper) 템플릿(Result, Error Code 등) 작성
- [ ] MOCK 데이터 연동을 위한 타입 익스포트(Export) 설정
- [ ] Swagger 혹은 JSDoc 규격을 사용해 시스템 API Docs에 기록 명세

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 타입 안정성 검증
- Given: 정의된 Zod Schema(또는 Interface)가 포함된 코드 블록
- When: 클라이언트 측에서 잘못된 타입(예: number 대신 string)을 Request DTO로 주입하려 시도함
- Then: TypeScript 컴파일 타임에 강력한 Type Error가 발생하여 문제를 차단한다.

Scenario 2: 런타임 DTO 검증(Validation) 통과
- Given: 올바른 형식의 `uploadAndParseExcel` DTO 페이로드
- When: Server Action 도입부에서 페이로드 런타임 검증(safeParse 등)을 실행함
- Then: 검증을 통과하고 비즈니스 로직으로 정상 이관되는 타입 추론 객체를 반환한다.

## :gear: Technical & Non-Functional Constraints
- 성능: 런타임 검증 비용이 응답 지연에 영향을 미치지 않도록 경량 스키마 검증기(Zod) 사용.
- 안정성: 에러 응답 구조가 일관된 공통 응답 DTO 규격을 따를 것.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 작성된 DTO 모델들이 컴파일 타임에 100% Type-safe한 구조로 이뤄졌는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?
- [ ] API 반환 명세가 SRS 6.1의 요구사항을 오류 없이 충족하는가?

## :construction: Dependencies & Blockers
- Depends on: #DB-002 (테이블 스키마 구조)
- Blocks: #CMD-001, #CMD-002

---

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] CMD-001: 브라우저(Client) 기반 SheetJS 활용 초기 엑셀 데이터 파싱 및 전송 제어 로직 구현"
labels: 'feature, frontend, priority:high, core-logic'
assignees: ''
---

## :dart: Summary
- 기능명: [CMD-001] Client-side 엑셀 파싱 및 최적화 전송 로직 구현
- 목적: 서버 연산 및 타임아웃 부하 방지를 위해 브라우저 단에서 SheetJS를 활용하여 비표준 엑셀 파일을 JSON으로 1차 가공 후 Server Action으로 전송한다.

## :link: References (Spec & Context)
> :bulb: AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- SRS 문서: [`/docs/SRS_v1.0.md#631-엑셀-클라이언트-파싱-및-server-action-매핑-로직-vercel-timeout-회피형`](#)
- 통신 명세: `uploadAndParseExcel` DTO Contract (#API-001)

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] `xlsx` (SheetJS) 라이브러리 설치 및 최적화 설정
- [ ] 파일 읽기(File Reader) 파이프라인 및 Drag&Drop 뷰 컴포넌트 구성 연동
- [ ] 브라우저 환경에서 추출된 엑셀 바이너리를 원시 JSON 데이터(Array of Objects)로 포맷팅
- [ ] 서버리스 환경(Vercel 함수 10~15초 제한) 대비 Chunking 등 분할 전송 제어 로직 기획 및 구현
- [ ] React UI 내 업로드/파싱 로딩 상태 관리 및 프로그레스 통지 피드백 구현

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 로컬 엑셀 파일 JSON 신속 변환
- Given: 비표준 컬럼 및 5,000 Row 이상의 데이터가 담긴 엑셀 파일(.xlsx)
- When: 사용자가 업로드 UI 구역에 파일을 드래그 & 드랍함
- Then: 브라우저 메모리 내 수 초 이내에 에러 없이 JSON 배열 형태로 파싱이 정상 완료된다.

Scenario 2: 대용량 데이터 분할 Chunking 전송
- Given: 파싱 결과 용량이 5MB를 초과하는 대규모 JSON 객체 확보 상태
- When: 파일 전송 및 처리 요청 버튼을 클릭함
- Then: Vercel Payload 제한 회피를 위해 적절히 분할 전송되어 Server Action 호출(HTTP 200 수준 반환)을 지연 타임아웃 없이 달성한다.

## :gear: Technical & Non-Functional Constraints
- 성능: 브라우저 UI Main 스레드를 블로킹(Freeze)하지 않기 위해 필요 시 Web Worker 검토 (또는 비동기 처리 적용).
- 보안: 악성 엑셀 파일(Macro 스크립트 등) 삽입 시 자동 실행을 차단하고 철저히 바이너리 셀 데이터만 추출.

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 파싱 및 Chunking 전송 과정의 Unit Test가 추가되었고 통과하는가?
- [ ] 대용량 파일 시뮬레이션에서 브라우저 크래시나 먹통 현상이 없는가?
- [ ] SonarQube / Linter 등의 정적 분석 도구 경고가 없는가?

## :construction: Dependencies & Blockers
- Depends on: #API-001 (수신부 DTO 계약 정의)
- Blocks: #CMD-002 (AI 스키마 매핑 비즈니스 로직)

---

---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Feature] INF-001: 단일 테넌트 기반 데이터 강제 논리 분리를 위한 DB 레벨의 Supabase PostgreSQL RLS Policy 단언 스크립트 작성"
labels: 'infra, security, database, priority:high'
assignees: ''
---

## :dart: Summary
- 기능명: [INF-001] Supabase RLS (Row Level Security) 기반 멀티테넌트 안전망 적용
- 목적: 애플리케이션(코드)단에서 접근 제어 누락이 발생하더라도, 타 기관의 성과 데이터가 무단 열람·수정될 수 없게 DB 계층에서 접근을 원천 차단하는 방어막 로직을 설계한다.

## :link: References (Spec & Context)
> :bulb: AI 아키텍트 주안점: RLS 구축 시 코드 복잡성을 줄이고 Join 퍼포먼스 드랍을 방어할 것.
- SRS 보안 요건: [`/docs/SRS_v1.0.md#req-func-039`](#) (멀티테넌트 분리 강제)
- 데이터 모델: `Organization`, `Upload`, `Dataset` 등

## :white_check_mark: Task Breakdown (실행 계획)
- [ ] Supabase Auth의 JWT 내 커스텀 클레임(`app_metadata->org_id`)에 인증된 사용자 소속 기관 ID 부여 메커니즘 확립
- [ ] 핵심 모델 테이블(Organization, User, Upload, Dataset, TalentProfile 등)에 대하여 RLS 강제 활성화 (`ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`) 적용 스크립트 도출
- [ ] 각 테이블별 SELECT, INSERT, UPDATE, DELETE RLS Policy 선언 (Session JWT의 `org_id` 와 Record의 `org_id` 비교)
- [ ] 시스템 관리자(SYSTEM_ADMIN) Role 할당 시 RLS 제약을 Bypass 하거나 관통할 수 있는 조건절 부여
- [ ] 생성된 스크립트를 Prisma Migration 구조(또는 .sql) 내에 편입시켜 자동 배포 체계 확보

## :test_tube: Acceptance Criteria (BDD/GWT)
Scenario 1: 타 기관 데이터 접근 시도 원천 차단
- Given: 기관 A(org_id='a123') 소속 권한을 가진 유저의 Session 토큰 환경
- When: 기관 B(org_id='b999')의 소유인 Dataset 레코드를 악의적인 방식(WHERE절 우회 등)으로 조회 또는 수정하려 시도함
- Then: DB 엔진 계층의 RLS 정책 차단으로 인해 0건 반환 또는 접근 권한 예외(Permission Denied)가 강제 발생한다.

Scenario 2: 소속 기관 내 정상적인 조회 및 조작
- Given: 기관 A(org_id='a123') 소속 유저의 정상 Auth Session
- When: 기관 A가 소속 권한으로 보유한 데이터 행(Upload/Dataset)의 조회/삽입 명령을 실행함
- Then: RLS 방어막을 통과하고 충돌 없이 성공 상태를 반환한다.

## :gear: Technical & Non-Functional Constraints
- 성능: RLS 조건식의 판 단계를 줄여(가령 Nested 쿼리 방지) Index Scan을 손상시키거나 쿼리 퍼포먼스가 저하되는 것을 막아야 함.
- 안정성: 테스트를 거치지 않은 Policy의 무분별한 운영 배포를 절대 금지함(테이블 완전 격리사고 방지).

## :checkered_flag: Definition of Done (DoD)
- [ ] 모든 Acceptance Criteria를 충족하는가?
- [ ] 핵심 공통 테이블 및 파이프라인 테이블에 `ENABLE ROW LEVEL SECURITY`가 적용되었는가?
- [ ] RLS 격리 단위 Test Tool 등을 활용하여 타 기관 데이터 누출 차단 여부가 자동 검증되었는가?

## :construction: Dependencies & Blockers
- Depends on: #DB-001, #DB-002 (스키마 및 구조 완성 필수)
- Blocks: 시스템 전역의 모든 사용자 데이터 조회 및 Action 로직 (최상위 Blocker)

---
