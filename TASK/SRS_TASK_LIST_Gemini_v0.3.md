# SRS-001 개발 태스크 리스트 (Epic & Feature 단위 분해)

본 문서는 AI·xAPI 기반 인재양성 사업 통합 운영 허브 플랫폼(SRS-001, v0.3)을 기반으로, 백엔드 및 AI 에이전트 주도 개발(Vibe Coding)에 최적화되도록 도출된 실행 가능한 개발 태스크 리스트입니다.

- **작성 원칙**:
  1. 데이터(Data) & 인터페이스(Contract) 먼저 구축 (단일 진실 공급원 확보)
  2. 조회(Query)와 상태 변경(Command) 분리를 통한 문맥 최소화
  3. 모든 인수 조건(AC)을 테스트 주도(TDD) 작업으로 변환
  4. 비기능적 요구사항(CSAP, 배포 제약 등) 인프라 작업화

## 전체 개발 태스크 보드 (Task Board)

| Task ID | Epic (도메인) | Feature (기능명) | 관련 SRS 섹션 | 선행 태스크 (Dependencies) | 복잡도 (H/M/L) |
|---|---|---|---|---|---|
| **Step 1. 계약 및 규약(Contract/Protocol) 및 데이터(Data) 명세 Task** | | | | | |
| DB-001 | System Core | [DB] User, Organization, Auth, AuditLog 등 공통 기초 테이블 스키마 작성 및 Prisma 세팅 | 6.2.1 | None | L |
| DB-002 | Data Hub | [DB] Upload, MappingPattern, Dataset, CleansingLog 데이터 수집 관련 테이블 스키마 작성 | 6.2.1 | DB-001 | M |
| DB-003 | Report | [DB] 무결성 Report, 알림(Notification) 및 스케줄 상태 테이블 스키마 작성 | 6.2.1 | DB-002 | L |
| DB-004 | Talent & Badge | [DB] Badge, BadgeClass, TalentProfile, ViewingCredit 인재 관리 테이블 스키마 작성 | 6.2.1 | DB-001 | M |
| API-001 | Data Hub | [API Spec] 엑셀 업로드, AI 매핑 승인, 병합, 데이터 롤백 Server Action Request/Response DTO 정의 | 3.3, 6.1 | DB-002 | M |
| API-002 | Report | [API Spec] 무결성 검증, 리포트 생성 및 저장 Action 및 LTI 모의 연동 Request/Response DTO 정의 | 3.3, 6.1 | DB-003 | L |
| API-003 | Talent & Badge | [API Spec] 배지 발급/검증, 인재 검색, 크레딧 차감 상세 열람 Action Request/Response DTO 정의 | 3.3, 6.1 | DB-004 | M |
| MOCK-001 | Data Hub | [Mock] 프론트엔드 UI 개발 병렬 처리를 위한 엑셀 업로드 성공/실패 시나리오 DTO 테스트용 Mock Data 세팅 | F1, F2 | API-001 | L |
| MOCK-002 | Talent & Badge | [Mock] 인재 검색 화면, 배지 프로필 UI 디스플레이 렌더링용 Mock Data 세팅 | F4, F5 | API-003 | L |
| **Step 2. 로직(Logic) 및 상태 변경(Mutation) 분해 Task (CQRS 활용)** | | | | | |
| QRY-001 | System Core | [Feature/Query] 통합 시스템 대시보드 지표(조회수, 처리시간, 품질점수) 집계 및 렌더링 로직 | F6 | API-001 | M |
| QRY-002 | Data Hub | [Feature/Query] 통합/정제 데이터 항목별 필터링 기능 및 이전 위탁기관용 매핑 패턴 조회 검색 로직 | F2 | DB-002, MOCK-001 | M |
| QRY-003 | Talent & Badge | [Feature/Query] 직무, 획득 Badge 조건 기반의 인재 검색 카탈로그 목록 반환 로직 | F5 | DB-004, MOCK-002 | M |
| CMD-001 | Data Hub | [Feature/Command] 브라우저(Client) 기반 SheetJS 활용 초기 엑셀 데이터 파싱 및 통신 규격 변환, 전송 제어 로직 구현 | F1 | API-001 | H |
| CMD-002 | Data Hub | [Feature/Command] Vercel AI SDK(Gemini) 호출 기반 업로드 파일 비표준 스키마 자동 매핑 처리 및 DB(Dataset) 적재 로직 | F1, F2 | CMD-001 | H |
| CMD-003 | Data Hub | [Feature/Command] 데이터 일괄 병합 로직(Transaction 단위) 및 결측치/이상치 플래그 삽입 자동 클렌징 처리 로직 | F2 | CMD-002 | H |
| CMD-004 | Report | [Feature/Command] 데이터 무결성 검증과 동시에 PDF/Excel 리포트 파일 생성, Supabase Storage 병합/저장 및 접근 링크 반환 | F3 | API-002, CMD-003 | M |
| CMD-005 | Talent & Badge | [Feature/Command] xAPI 이력 기반 조건(Local Fit) 분석에 따른 Open Badge 2.0 발행 규칙 평가 및 Assertion 증명서 발급 상태 기록 | F4 | API-003 | H |
| CMD-006 | Talent & Badge | [Feature/Command] 기업(HR)의 인재 목록 상세 프로필 조회 시 Viewing Credit 잔액 검증 후 차감하는 Transaction 및 조회 내역 저장 | F5 | API-003 | M |
| **Step 3. 완료 조건(AC) 기반 자동화 테스트(Test) 작성 Task** | | | | | |
| TST-001 | Data Hub | [Test] 10건 동시 파일 일괄 업로드 처리 여부 점검 및 포맷 에러, 용량 초과 발생 시 에러 Throwing 작동 단위 테스트 작성 | F1, AC | CMD-001 | M |
| TST-002 | Data Hub | [Test] 기관별 수동 보정을 마친 이전 매핑 패턴이 신규 업로드 진행 시 유효하게 자동 적용되는지 검토하는 통합 테스트 | F1, AC | CMD-002 | M |
| TST-003 | Data Hub | [Test] 데이터셋 롤백 실행 트리거 시 Version History 명세에 기반하여 직전 상태로 DB 레코드가 복구되는지 검증 | F2, AC | CMD-003 | H |
| TST-004 | Report | [Test] 리포트 내 오류(수식, 데이터 등) 존재 시 생성이 중단되는 에러 발생 예외 분기 테스트 자동화 | F3, AC | CMD-004 | M |
| TST-005 | Talent & Badge | [Test] 사용자 활동 모의 xAPI 레코드 주입 환경 하에서 Local Fit 자격 달성 여부 시스템 판정 결과 테스트 구동 | F4, AC | CMD-005 | H |
| TST-006 | Talent & Badge | [Test] 기관 보유 열람권 크레딧 부족(잔액 0) 시 인재 프로필 정보 반환 차단 및 DB Action Transaction 롤백 정상 작동 여부 유닛 테스트 | F5, AC | CMD-006 | M |
| **Step 4. 비기능 제약(NFR) 인프라 및 보안 설정 Task** | | | | | |
| INF-001 | Infrastructure | [Sec] 단일 테넌트 기반 데이터 강제 논리 분리를 위한 DB 레벨의 Supabase PostgreSQL RLS(Row Level Security) Policy 스크립트 작성 | 4.1.8 | DB-001 | H |
| INF-002 | Infrastructure | [Sec] Next.js App Router와 통합되는 Supabase Auth 연동 미들웨어 및 Role 기반 접근 분리(RBAC) 체계 구현 | 제약 조건 | INF-001 | H |
| INF-003 | Infrastructure | [Infra] 플랫폼 전역의 생성/조회/수정 활동을 가로채어 규격화하는 xAPI 호환 구조의 추적 로깅(AuditLog) 미들웨어 연동 | 4.1.8 | INF-002 | M |
| INF-004 | Infrastructure | [Infra] API 지연 대응(NFR) 목적 k6 스크립트 작성. 서버 타임아웃 한계 및 동시 100건 접근 허용 등 부하 임계점 사전 확인 | PRD NFR | CMD-002 | M |
| INF-005 | Infrastructure | [UI] 프로젝트 스캐폴딩 설정 및 Tailwind CSS / shadcn-ui 공통 디자인 시스템 컴포넌트 셋업 구성 | C-TEC-004 | None | L |
