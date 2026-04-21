# SRS-001 검토 결과 보고서

**검토 대상 문서:** SRS-001_VPS_v1.0_Opus.md (SRS-001 v1.0, 1,002 lines)
**기준 문서 (PRD):** VPS_v1.0.md (VPS v1.0)
**검토 기준:** ISO/IEC/IEEE 29148:2018 및 사용자 지정 8개 검증 항목
**검토 일자:** 2026-04-15
**검토자:** Senior Requirements Engineer (Automated Review)

---

## 종합 평가 요약

| # | 검증 항목 | 판정 | 비고 |
|:---:|:---|:---:|:---|
| ① | PRD Story·AC → REQ-FUNC 반영 | ✅ 적합 | JTBD-1~4 + F1~F7 → REQ-FUNC-001~044 (44개) |
| ② | KPI·성능 목표 → REQ-NF 반영 | ✅ 적합 | O1~O6 + DV-1~4 → REQ-NF-001~024 (24개) |
| ③ | API 목록 → 인터페이스 섹션 반영 | ✅ 적합 | §3.3 + §6.1 이중 기재 (38개 엔드포인트) |
| ④ | 엔터티·스키마 → Appendix 완성 | ✅ 적합 | 12개 Entity 완전 정의, ERD 포함 |
| ⑤ | Traceability Matrix 누락 여부 | ✅ 적합 | 68개 매핑 엔트리 (REQ-FUNC-001~044 + REQ-NF-001~024) |
| ⑥ | 핵심 다이어그램 포함 여부 | ✅ 적합 | ERD ✅, Sequence ✅, **Use Case ✅, Class ✅, Component ✅ — 2026-04-15 보완 완료** |
| ⑦ | Sequence Diagram 3~5개 포함 | ✅ 적합 | 총 7개 시퀀스 다이어그램 포함 (핵심 3 + 상세 4) |
| ⑧ | ISO 29148 구조 준수 | ✅ 적합 | 6개 섹션 구조 준수 |

**종합 판정: ✅ 적합 (Conformant)**

> 8개 검증 항목 전체 적합.
> 2026-04-15 보완 작업으로 Use Case Diagram, Class Diagram, Component Diagram 3종이 SRS에 추가되어 완전 적합 달성.

---

## 1. PRD Story·AC → REQ-FUNC 반영 검증

### 1.1 JTBD → REQ-FUNC Source 추적 결과

| PRD Story | SRS 반영 REQ-FUNC | 판정 |
|:---|:---|:---:|
| **JTBD-1** (김지수 / 데이터 수합·통합·대시보드) | REQ-FUNC-001~018, 029~034 | ✅ |
| **JTBD-2** (최수영 / Local Fit Badge·인재 매칭) | REQ-FUNC-019~028 | ✅ |
| **JTBD-3** (강은지 / xAPI Skill-gap 진단) | Phase 2 예약(IS-08, IS-09)으로 Scope에 명시, Phase 1 REQ-FUNC 미생성 — 설계 의도 적합 | ✅ |
| **JTBD-4** (신동민 / CSAP·보안 감사) | REQ-FUNC-035~037 (F7 LTI 연동), CON-01 (CSAP 제약) | ✅ |

### 1.2 Feature → REQ-FUNC 분해 결과

| PRD Feature | MoSCoW | SRS REQ-FUNC | 분해 수 | 판정 |
|:---|:---:|:---|:---:|:---:|
| F1. AI 스마트 업로더 | Must | REQ-FUNC-001~006 | 6개 | ✅ |
| F2. 통합 데이터 허브 | Must | REQ-FUNC-007~012 | 6개 | ✅ |
| F3. 무결성 리포트 | Must | REQ-FUNC-013~018 | 6개 | ✅ |
| F4. Local Fit Badge | Must | REQ-FUNC-019~023 | 5개 | ✅ |
| F5. 인재 열람권 | Should | REQ-FUNC-024~028 | 5개 | ✅ |
| F6. ROI 대시보드 | Must | REQ-FUNC-029~034 | 6개 | ✅ |
| F7. LTI 연동 (조건부) | Should | REQ-FUNC-035~037 | 3개 | ✅ |
| 공통 (인증·권한·멀티테넌시) | Must | REQ-FUNC-038~044 | 7개 | ✅ |
| F8~F12 (Phase 2~3) | Could/Won't | Scope에 명시, REQ 미생성 | — | ✅ 의도적 |

### 1.3 AC(Acceptance Criteria) 형식 확인

| 검증 항목 | 결과 |
|:---|:---|
| Given/When/Then 형식 적용 | ✅ 44개 REQ-FUNC 전체 적용 |
| 정량적 수치 기준 포함 | ✅ 시간(5분, 1초, 3분 등), 정확도(90%), 건수(10개) 등 |
| 부정 시나리오(Negative Test) 포함 | ✅ REQ-FUNC-005(업로드 실패), 018(검증 실패), 038(권한 부족 403) |

### 1.4 관찰사항

| # | 관찰 내용 | 심각도 |
|:---:|:---|:---:|
| O-1 | PRD §9 Track 2 "채용 성공 수수료(Success-fee)" 과금 로직이 REQ-FUNC에 별도 분해 없음 (REQ-FUNC-025 열람권만 존재) | 낮음 |
| O-2 | JTBD-3(강은지) → Phase 2 예약 연결이 Traceability Matrix에 별도 행 미기재 | 낮음 |

---

## 2. KPI·성능 목표 → REQ-NF 반영 검증

### 2.1 PRD Desired Outcome → REQ-NF 매핑 결과

| PRD Outcome | AS-IS | TO-BE | SRS REQ-NF | 목표값 일치 | 판정 |
|:---|:---:|:---:|:---|:---:|:---:|
| **O1** 데이터 취합 소요 시간 | 48h/월 | ≤ 1h/월 | REQ-NF-001 | ✅ | ✅ |
| **O2** 조기 이탈률 감소 | ~50% | ≤ 10% | REQ-NF-023 | ✅ | ✅ |
| **O3** 보고서 에러 건수 | 월 1~2건 | 0건 | REQ-NF-022 | ✅ | ✅ |
| **O4** 역량 배지 신뢰도 | 종이 수료증 | xAPI Badge | REQ-FUNC-019~023 | ✅ 기능요구 반영 | ✅ |
| **O5** 허수 검토 시간 | ~80% | ≤ 20% | REQ-NF-024 | ✅ | ✅ |
| **O6** 보안 감사 이상무 | 이상무 유지 | CSAP 대응 자동 보고 | CON-01, IS-10~11 | ✅ Phase 2 반영 | ✅ |

### 2.2 PRD Differential Value → REQ-NF 매핑 결과

| PRD DV | 수치 | SRS REQ-NF | 판정 |
|:---|:---|:---|:---:|
| DV-1 취합 속도 48배 | 48h → 1h | REQ-NF-001 | ✅ |
| DV-2 에러율 100% 개선 | 1~2건 → 0건 | REQ-NF-022 | ✅ |
| DV-3 이탈 방어 5배 | 50% → 10% | REQ-NF-023 | ✅ |
| DV-4 허수 필터링 4배 | 80% → 20% | REQ-NF-024 | ✅ |

### 2.3 성능 범주별 REQ-NF 분포

| 성능 범주 | REQ-NF 수 | 주요 항목 | 판정 |
|:---|:---:|:---|:---:|
| 성능 (p95, latency, throughput) | 7개 | REQ-NF-001~007 | ✅ |
| 가용성 (SLA, RPO, RTO) | 4개 | REQ-NF-008~011 | ✅ |
| 보안 (TLS, RBAC, 암호화, 감사 로그) | 5개 | REQ-NF-012~016 | ✅ |
| 비용 | 1개 | REQ-NF-017 | ✅ |
| 운영/모니터링 | 2개 | REQ-NF-018~019 | ✅ |
| 확장성/유지보수성 | 2개 | REQ-NF-020~021 | ✅ |
| 비즈니스 성과 KPI | 3개 | REQ-NF-022~024 | ✅ |
| **합계** | **24개** | — | ✅ |

### 2.4 관찰사항

| # | 관찰 내용 | 심각도 |
|:---:|:---|:---:|
| O-3 | CSAP 인증 달성 시기가 REQ-NF에 명시적 측정 기준으로 미정의 (CON-01 제약사항으로 간접 반영) | 낮음 |
| O-4 | 멀티테넌트 테넌트당 원가 상한 등 구체적 비용 임계치 미정의 | 낮음 |

---

## 3. API 목록 → 인터페이스 섹션 반영 검증

### 3.1 이중 기재 확인

| 위치 | 내용 | 판정 |
|:---|:---|:---:|
| §3.3 API Overview | 9개 그룹 요약 (엔드포인트 패턴, 프로토콜, 인증 방식) | ✅ |
| §6.1 API Endpoint List | 38개 상세 엔드포인트 (HTTP Method, 경로, 인증, 관련 Req) | ✅ |

### 3.2 API 그룹별 완전성

| API 그룹 | §3.3 | §6.1 엔드포인트 수 | REQ-FUNC 연결 | 판정 |
|:---|:---:|:---:|:---:|:---:|
| 데이터 업로드 (`/api/v1/upload/*`) | ✅ | 5개 (#1~5) | 001~006 | ✅ |
| 데이터 허브 (`/api/v1/hub/*`) | ✅ | 7개 (#6~12) | 007~012 | ✅ |
| 리포트 (`/api/v1/reports/*`) | ✅ | 4개 (#13~16) | 013~018 | ✅ |
| Badge (`/api/v1/badges/*`) | ✅ | 6개 (#17~22) | 019~023 | ✅ |
| 인재 열람 (`/api/v1/talent/*`) | ✅ | 3개 (#23~25) | 024~028 | ✅ |
| 대시보드 (`/api/v1/dashboard/*`) | ✅ | 5개 (#26~30) | 029~034 | ✅ |
| LTI 연동 (`/lti/v1.3/*`) | ✅ | 2개 (#31~32) | 035~037 | ✅ |
| xAPI 프록시 (`/xapi/v1/*`) | ✅ | 2개 (#33~34) | 010,016,027 | ✅ |
| 관리 (`/api/v1/admin/*`) | ✅ | 4개 (#35~38) | 039,040,043,NF-018 | ✅ |

### 3.3 관찰사항

| # | 관찰 내용 | 심각도 |
|:---:|:---|:---:|
| O-5 | 시퀀스 다이어그램 §6.3.3에 표현된 `/api/v1/talent/{profileId}/interview` 엔드포인트가 §6.1 API Endpoint List에는 미등재 | 낮음 |

---

## 4. 엔터티·스키마 → Appendix 완성 검증

### 4.1 Entity 정의 완전성

| Entity | 필드 수 | PK | FK | ENUM | 판정 |
|:---|:---:|:---:|:---:|:---:|:---:|
| Organization (기관) | 8 | ✅ | — | ✅ org_type, tier | ✅ |
| User (사용자) | 11 | ✅ | ✅ org_id | ✅ role | ✅ |
| Upload (파일 업로드) | 12 | ✅ | ✅ user_id, org_id | ✅ file_format, status | ✅ |
| MappingPattern (매핑 패턴) | 8 | ✅ | ✅ org_id | — | ✅ |
| Dataset (통합 데이터셋) | 10 | ✅ | ✅ org_id | ✅ status | ✅ |
| CleansingLog (클렌징 로그) | 10 | ✅ | ✅ dataset_id, resolved_by | ✅ error_type, action | ✅ |
| Report (리포트) | 10 | ✅ | ✅ dataset_id, generated_by | ✅ report_type, status | ✅ |
| Badge (Open Badge) | 8 | ✅ | ✅ learner_id, badge_class_id | — | ✅ |
| BadgeClass (Badge 유형) | 8 | ✅ | ✅ issuer_org_id | — | ✅ |
| TalentProfile (인재 프로필) | 9 | ✅ | ✅ user_id | — | ✅ |
| ViewingCredit (열람권) | 7 | ✅ | ✅ org_id | — | ✅ |
| AuditLog (감사 로그) | 10 | ✅ | ✅ user_id | — | ✅ |

**총 12개 Entity, 111개 필드** — 완전 정의 확인 ✅

### 4.2 ERD 포함 확인

- §6.2.2에 Mermaid `erDiagram` 형식 ERD 포함 ✅
- 12개 Entity 간 관계(1:N, 1:1) 정의 완료 ✅

### 4.3 관찰사항

| # | 관찰 내용 | 심각도 |
|:---:|:---|:---:|
| O-6 | Report Entity의 `schedule_id` FK 대상인 `ReportSchedule` Entity가 별도 테이블로 정의되지 않음 | 낮음 |

---

## 5. Traceability Matrix 누락 여부 검증

### 5.1 매핑 범위

| 매핑 유형 | 엔트리 수 | 판정 |
|:---|:---:|:---:|
| Story/Source → REQ-FUNC → TC | 44개 | ✅ |
| Source → REQ-NF → TC-NF | 24개 | ✅ |
| **총 엔트리** | **68개** | ✅ |

### 5.2 완전성 검증

| 검증 항목 | 결과 | 판정 |
|:---|:---|:---:|
| 모든 REQ-FUNC-001~044에 대응 TC 존재 | TC-001 ~ TC-044 전수 매핑 | ✅ |
| 모든 REQ-NF-001~024에 대응 TC-NF 존재 | TC-NF-001 ~ TC-NF-024 전수 매핑 | ✅ |
| 모든 REQ에 Source(Story/PRD 출처) 명시 | 전수 Source 컬럼 기입 확인 | ✅ |
| 역추적: PRD JTBD-1 → REQ-FUNC | JTBD-1 → 001~018, 029~034 연결 확인 | ✅ |
| 역추적: PRD JTBD-2 → REQ-FUNC | JTBD-2 → 019~028 연결 확인 | ✅ |
| 역추적: PRD JTBD-4 → REQ-FUNC | JTBD-4 → 035~037 연결 확인 | ✅ |

---

## 6. 핵심 다이어그램 포함 여부 검증

### 6.1 다이어그램 유형별 포함 현황

| 다이어그램 유형 | 포함 여부 | 위치 | 수량 | 판정 |
|:---|:---:|:---|:---:|:---:|
| **ERD (Entity Relationship Diagram)** | ✅ | §6.2.2 | 1개 | ✅ |
| **Sequence Diagram** | ✅ | §3.4 (3개) + §6.3 (4개) | 7개 | ✅ |
| **Use Case Diagram** | ✅ | §3.4 | 1개 | ✅ (2026-04-15 추가) |
| **Class Diagram** | ✅ | §6.2.3 | 1개 | ✅ (2026-04-15 추가) |
| **Component Diagram** | ✅ | §3.5 | 1개 | ✅ (2026-04-15 추가) |

### 6.2 누락 다이어그램 상세 분석 및 권고

#### ❌ Use Case Diagram

- **필요 근거**: 5개 Actor(김지수, 최수영, 학습자, 시스템관리자, 외부LMS)와 8개 Use Case(F1~F7 + 공통관리) 간 관계를 시각화해야 이해관계자별 시스템 사용 범위를 한눈에 파악 가능
- **권고 형식**: Mermaid `flowchart` 또는 PlantUML Use Case 형식
- **적절 위치**: §3 System Context and Interfaces 내 추가

#### ❌ Class Diagram

- **필요 근거**: §6.2의 Entity 테이블이 데이터 모델을 정의하나, 서비스 계층의 객체 간 상속·연관·의존 관계(UploadService, DataHubService, ReportService, BadgeService, TalentService 등)을 UML Class Diagram으로 표현해야 설계 의도가 명확해짐
- **권고 형식**: Mermaid `classDiagram`
- **적절 위치**: §6 Appendix (§6.2.3으로 추가)

#### ❌ Component Diagram

- **필요 근거**: §3.1 External Systems와 §3.2 Client Applications에 외부 시스템과 클라이언트가 목록화되었으나, 시스템 내부 컴포넌트의 계층 구조(Client → API Gateway → 마이크로서비스 → 데이터 저장소 → 외부 시스템) 배포 단위 및 의존 관계 시각화 부재
- **권고 형식**: Mermaid `flowchart TB` (계층형)
- **적절 위치**: §3 System Context and Interfaces 내 추가

---

## 7. Sequence Diagram 3~5개 포함 검증

### 7.1 시퀀스 다이어그램 목록

| # | 위치 | 제목 | 커버 기능 | Mermaid | 판정 |
|:---:|:---|:---|:---|:---:|:---:|
| 1 | §3.4.1 | AI 스마트 업로더 데이터 자동 수집·정제 | F1 + F2 | ✅ | ✅ |
| 2 | §3.4.2 | 감사 대응 무결성 리포트 자동 생성 | F3 | ✅ | ✅ |
| 3 | §3.4.3 | Local Fit Open Badge 발급 및 인재 매칭 | F4 + F5 | ✅ | ✅ |
| 4 | §6.3.1 | End-to-End: 업로드 → AI 매핑 → 정제 → 리포트 | F1+F2+F3 (상세) | ✅ | ✅ |
| 5 | §6.3.2 | Local Fit Badge 발급 (자동 판정 프로세스) | F4+F7 (상세) | ✅ | ✅ |
| 6 | §6.3.3 | 기업 HR 인재 열람 → 관심 표시 → 면접 전환 추적 | F5 (상세) | ✅ | ✅ |
| 7 | §6.3.4 | ROI·성과 대시보드 데이터 집계 | F6 (상세) | ✅ | ✅ |

**총 7개** — 기준(3~5개) **초과 충족** ✅

### 7.2 시퀀스 다이어그램 품질 평가

| 평가 항목 | 결과 |
|:---|:---|
| Actor 명시 (페르소나 연결) | ✅ 김지수, 최수영, 학습자, 관리자 |
| API 엔드포인트 명시 | ✅ 실제 REST 경로 포함 |
| 서비스 간 메시지 흐름 | ✅ 동기/비동기 구분 |
| 분기 처리 (alt/else) | ✅ 오류/규칙 충족/검증 통과 분기 |
| 병렬 처리 (par) | ✅ §6.3.4 대시보드 6개 병렬 쿼리 |
| xAPI Statement 기록 | ✅ 주요 이벤트마다 LRS 기록 단계 포함 |

---

## 8. ISO/IEC/IEEE 29148:2018 구조 준수 검증

### 8.1 섹션 구조 대조

| ISO 29148 권장 섹션 | SRS 대응 | 판정 |
|:---|:---|:---:|
| **1. Introduction** (Purpose, Scope, Definitions, References) | §1 (1.1~1.4) | ✅ |
| **2. Stakeholders / Overall Description** | §2 Stakeholders | ✅ |
| **3. System Requirements / Architecture** | §3 System Context and Interfaces (3.1~3.4) | ✅ |
| **4. Specific Requirements** (Functional + Non-Functional) | §4 (4.1~4.2) | ✅ |
| **5. Verification / Validation** (Traceability) | §5 Traceability Matrix | ✅ |
| **6. Appendix** | §6 (6.1~6.4) | ✅ |
| Document Control (Version History) | Document Control 테이블 | ✅ |

### 8.2 ISO 29148 핵심 요소 적합도

| ISO 29148 요소 | SRS 반영 현황 | 판정 |
|:---|:---|:---:|
| Uniquely identifiable requirements | REQ-FUNC-xxx / REQ-NF-xxx 체계 | ✅ |
| Testable requirements | 모든 REQ에 AC(Given/When/Then) 또는 정량 목표값 | ✅ |
| Traceable requirements | §5: Story ↔ Req ↔ TC 양방향 추적 | ✅ |
| Prioritized requirements | MoSCoW(Must/Should/Could/Won't) 컬럼 | ✅ |
| Constraints & Assumptions | §1.2.3 CON-01~05, ASM-01~05 | ✅ |
| External interface requirements | §3.1 External Systems, §3.3 API Overview | ✅ |
| Non-ambiguous language | 모호 표현("빠르게", "적절히" 등) 사용 0건 | ✅ |
| Atomic requirements | 1 REQ = 1 요구사항 원칙 준수 | ✅ |

### 8.3 관찰사항

| # | 관찰 내용 | 심각도 |
|:---:|:---|:---:|
| O-7 | "System Modes and States" 섹션 미분리. Upload Entity status ENUM이 상태 전이를 암시하나 State Transition Diagram 부재 | 낮음 |
| O-8 | Test Case ID(TC-001~TC-NF-024)의 실제 테스트 시나리오 상세는 별도 문서 위임 | 정보 |

---

## 종합 개선 권고 사항

### 필수 보완 (Must Fix) — 1건

| 우선순위 | 항목 | 설명 | 영향 검증 항목 |
|:---:|:---|:---|:---|
| ~~**P1**~~ | ~~Use Case / Class / Component Diagram 3종 추가~~ | ~~Mermaid 형식으로 §3 또는 §6에 3종 다이어그램 추가~~ | ✅ **완료 (2026-04-15)** |

### 권장 보완 (Should Fix) — 3건

| 우선순위 | 항목 | 설명 | 관련 관찰 |
|:---:|:---|:---|:---|
| **P2** | §6.1에 `/api/v1/talent/{profileId}/interview` 엔드포인트 추가 | 시퀀스 다이어그램 §6.3.3에서 사용되나 API 목록 미등재 | O-5 |
| **P3** | ReportSchedule Entity 정의 추가 | Report Entity schedule_id FK 대상 미정의 | O-6 |
| **P4** | 핵심 Entity State Transition Diagram 추가 | Upload/Dataset/Report 상태 전이 시각화 | O-7 |

### 선택 보완 (Could Fix) — 4건

| 우선순위 | 항목 | 설명 | 관련 관찰 |
|:---:|:---|:---|:---|
| P5 | 채용 성공 수수료 과금 REQ-FUNC 추가 (Phase 2 대비) | Track 2 과금 로직 | O-1 |
| P6 | Traceability Matrix에 Phase 2 예약 항목 행 추가 | JTBD-3 → F8/F9 연결 | O-2 |
| P7 | CSAP 인증 달성 마일스톤 REQ-NF 추가 | Phase 2 인증 시기 정량화 | O-3 |
| P8 | 멀티테넌트 테넌트당 비용 임계치 정의 | 운영 비용 관리 기준 | O-4 |

---

*— 검토 보고서 종료 —*
*검토 대상: SRS-001 v1.0 (SRS-001_VPS_v1.0_Opus.md, 1,002 lines)*
*검토 기준: VPS v1.0 + ISO/IEC/IEEE 29148:2018 + 사용자 지정 8개 검증 항목*
*작성일: 2026-04-15*
