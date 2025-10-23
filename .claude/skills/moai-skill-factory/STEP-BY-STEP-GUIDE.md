# 📚 Skill Factory 병렬 분석 - 단계별 상세 가이드

**목표**: Skill-factory 에이전트를 사용하여 기존 스킬 폴더들을 병렬로 분석하고 개선사항을 도출

---

## 🎯 전체 플로우 (3 Steps)

```
Step 1️⃣  → 분석 대상 선정 (Tier별 대표 스킬)
         ↓
Step 2️⃣  → 병렬 에이전트 분석 실행 (동시 실행)
         ↓
Step 3️⃣  → 결과 통합 및 보고서 작성
```

---

## Step 1️⃣: 분석 대상 선정

### 목표
✅ 각 계층별 대표 스킬 선정
✅ 분석할 4개 스킬 확정
✅ 분석 기준 정의

### 실행 방법

#### 1-1) 스킬 폴더 구조 파악
```bash
# 명령어
find .claude/skills -type d -name "moai-*" | sort

# 출력 예시
.claude/skills/moai-foundation-trust
.claude/skills/moai-foundation-tags
.claude/skills/moai-alfred-tag-scanning
.claude/skills/moai-domain-backend
.claude/skills/moai-lang-python
... (50개 이상)
```

#### 1-2) 계층별 카테고리 분류
```
📦 Foundation Tier (6개)
  ├─ moai-foundation-trust       ← 선정 ⭐
  ├─ moai-foundation-tags
  ├─ moai-foundation-specs
  ├─ moai-foundation-ears
  ├─ moai-foundation-git
  └─ moai-foundation-langs

📦 Alfred Tier (11개)
  ├─ moai-alfred-tag-scanning    ← 선정 ⭐
  ├─ moai-alfred-code-reviewer
  ├─ moai-alfred-debugger-pro
  ├─ moai-alfred-language-detection
  └─ ... (7개 더)

📦 Domain Tier (10개)
  ├─ moai-domain-backend         ← 선정 ⭐
  ├─ moai-domain-frontend
  ├─ moai-domain-web-api
  ├─ moai-domain-security
  └─ ... (6개 더)

📦 Language Tier (23개)
  ├─ moai-lang-python            ← 선정 ⭐
  ├─ moai-lang-typescript
  ├─ moai-lang-go
  ├─ moai-lang-rust
  └─ ... (19개 더)
```

#### 1-3) 선정 기준
| 기준 | 설명 | 적용 |
|-----|------|------|
| **중요도** | 프로젝트 전반에 미치는 영향 | Foundation > Alfred > Domain > Language |
| **복잡도** | 문서 규모와 다루는 개념 범위 | 높을수록 분석 가치 증가 |
| **연계성** | 다른 스킬과의 관계 | 명확한 통합점 |

### 결과: 선정 스킬 (4개)
```
✅ moai-foundation-trust      (Foundation, 핵심 품질 원칙)
✅ moai-alfred-tag-scanning   (Alfred, 추적 시스템)
✅ moai-domain-backend        (Domain, 아키텍처)
✅ moai-lang-python           (Language, 최신 표준)
```

---

## Step 2️⃣: 병렬 에이전트 분석 실행

### 목표
✅ 4개 스킬을 **동시에** 분석
✅ 각 스킬별 완성도 점수 산출
✅ 개선사항 구체화

### 실행 방법

#### 2-1) 병렬 분석 에이전트 실행

**중요**: 다음 4개 Task를 **동일한 메시지**에서 한번에 호출
(이렇게 하면 Claude Code가 병렬로 동시 실행함)

```python
# 의사코드: 4개 에이전트 병렬 실행

Agent 1: Task(
  subagent_type="general-purpose",
  description="Foundation: Trust 스킬 분석",
  prompt="""
  파일: /Users/goos/MoAI/MoAI-ADK/.claude/skills/moai-foundation-trust/SKILL.md

  분석 항목:
  1. 메타데이터 (이름, 버전, 설명)
  2. 문서 구조 (섹션, 제목)
  3. 핵심 내용 (TRUST 5 원칙)
  4. 코드 예시 유무
  5. 완성도 점수 (0-100)

  JSON 형식으로 반환
  """
)

Agent 2: Task(
  subagent_type="general-purpose",
  description="Alfred: Tag-scanning 스킬 분석",
  prompt="..." # 동일 구조, 다른 파일
)

Agent 3: Task(
  subagent_type="general-purpose",
  description="Domain: Backend 스킬 분석",
  prompt="..." # 동일 구조, 다른 파일
)

Agent 4: Task(
  subagent_type="general-purpose",
  description="Language: Python 스킬 분석",
  prompt="..." # 동일 구조, 다른 파일
)
```

#### 2-2) 각 에이전트의 분석 항목

각 에이전트는 다음 항목을 분석합니다:

```json
{
  "skill_name": "스킬 이름",
  "category": "계층명",
  "metadata": {
    "name": "...",
    "description": "...",
    "allowed_tools": ["..."],
    "auto_load": "...",
    "trigger_cues": "..."
  },
  "structure": {
    "sections": ["메타데이터", "What it does", ...],
    "total_lines": 100,
    "has_yaml_frontmatter": true,
    "has_code_examples": true
  },
  "content_score": 75,
  "findings": [
    "✅ 강점 1",
    "✅ 강점 2",
    "⚠️ 약점 1",
    "❌ 심각한 문제"
  ],
  "recommendations": [
    "개선사항 1",
    "개선사항 2"
  ]
}
```

#### 2-3) 병렬 실행의 이점

| 측면 | 순차 실행 | 병렬 실행 | 개선도 |
|-----|---------|---------|--------|
| **소요 시간** | 60분 | 15분 | ⬇️ 75% 단축 |
| **시스템 효율** | 1개 에이전트 | 4개 동시 | ⬆️ 4배 |
| **분석 일관성** | 시간차 편향 | 동시성 보장 | ⬆️ 높음 |
| **Cross-Tier 패턴** | 제한적 | 종합적 | ⬆️ 강함 |

#### 2-4) 에이전트 실행 확인

각 에이전트는 다음 신호로 완료를 알립니다:

```bash
✅ Agent 1 완료: Foundation Trust 분석 (JSON 반환)
✅ Agent 2 완료: Alfred Tag-scanning 분석 (JSON 반환)
✅ Agent 3 완료: Domain Backend 분석 (JSON 반환)
✅ Agent 4 완료: Language Python 분석 (JSON 반환)

# 모든 에이전트가 동시에 완료될 때까지 대기
```

### 실행 결과 예시

#### 에이전트 1 출력 (Foundation Trust)
```json
{
  "skill_name": "moai-foundation-trust",
  "category": "Foundation",
  "content_score": 75,
  "findings": [
    "✅ YAML 메타데이터 완벽",
    "✅ TRUST 5원칙 명확",
    "⚠️ 구체적인 검증 명령어 부족"
  ],
  "recommendations": [
    "언어별 TRUST 검증 명령어 매트릭스 추가",
    "자동화 검증 스크립트 템플릿 제공"
  ]
}
```

#### 에이전트 2 출력 (Alfred Tag-scanning)
```json
{
  "skill_name": "moai-alfred-tag-scanning",
  "category": "Alfred",
  "content_score": 68,
  "findings": [
    "✅ CODE-FIRST 원칙 명확",
    "❌ 템플릿 파일 누락 (templates/tag-inventory-template.md)",
    "⚠️ 예시가 보일러플레이트"
  ],
  "recommendations": [
    "[CRITICAL] 누락 템플릿 파일 생성",
    "[HIGH] How it works 알고리즘 상세화"
  ]
}
```

#### 에이전트 3 & 4 (생략, 유사 구조)

---

## Step 3️⃣: 결과 통합 및 보고서 작성

### 목표
✅ 4개 분석 결과를 통합
✅ 계층별 패턴 발견
✅ 실행 가능한 권장사항 도출

### 실행 방법

#### 3-1) 점수 집계
```
Foundation (Trust):     75/100
Alfred (Tag-scanning):  68/100
Domain (Backend):       75/100
Language (Python):      85/100
────────────────────────────
평균:                   75.75/100  (B+ 등급)
```

#### 3-2) Cross-Tier 패턴 분석

**공통 강점** (모든 스킬):
```
✅ 메타데이터 구조 표준화 (YAML frontmatter)
✅ 문서 체계 일관성 (13개 표준 섹션)
✅ 다른 스킬과의 연계 명시
✅ Foundation 원칙 준수
```

**공통 약점** (모든 스킬):
```
❌ 코드 예시 부족 (이론 중심)
❌ 워크플로우 가이드 미흡 (도구 나열)
❌ 에러 처리 가이드 부재
❌ 템플릿/스크립트 미제공
❌ 예시 실전성 낮음
```

#### 3-3) 개선 우선순위 매트릭스

```
        영향도 높음
            ▲
            │
    TAG-    │  TRUST
    scanning│  ★    Python
    ★★★    │  ★★   (완성형)
            │
    Backend │
    ★★     │
            │
    ────────┼──────────► 노력 작음
          낮음   많음

범례:
★★★ = 임팩트 높고 노력 적음 (즉시 추진)
★★  = 임팩트 중간, 노력 필요 (계획)
★   = 임팩트 낮지만 선택사항 (여유시)
```

#### 3-4) 액션 플랜 수립

##### Phase 1: 긴급 (1주일)
```
Priority 1: TAG-scanning 누락 템플릿 생성
  파일: templates/tag-inventory-template.md
  내용: TAG 인벤토리 샘플 + 정상/깨진 TAG 예시
  노력: 2-3시간
  ROI: 매우 높음 (완성도 68→85)

Priority 2: Trust 검증 명령어 추가
  항목: 언어별 TRUST 검증 명령어 매트릭스
  예: Python pytest, Go go test, Rust cargo test
  노력: 2-3시간
  ROI: 높음 (모든 프로젝트 적용)

Priority 3: Backend 코드 예시 추가
  개수: 5개 (언어별 1개)
  예: Python FastAPI, Go Gin, Node.js Express
  노력: 3-4시간
  ROI: 중간 (백엔드 프로젝트에만 적용)
```

##### Phase 2: 진행 중 (2주일)
```
- TAG-scanning 실제 사용 사례 5개 추가
- Trust 자동화 검증 스크립트 템플릿
- Backend 보안 섹션 신설 (JWT, RBAC, Secrets)
```

##### Phase 3: 지속 (1개월)
```
- 모든 스킬에 에러 처리 가이드 추가
- CI/CD 파이프라인 예시 통합
- 각 스킬별 검증 테스트 작성
```

#### 3-5) 보고서 작성

결과를 Markdown 문서로 정리합니다:

```markdown
# 병렬 분석 보고서

## Executive Summary
- 분석 대상: 4개 스킬 (Foundation, Alfred, Domain, Language)
- 평균 점수: 75.75/100 (B+)
- 종합 평가: 구조적으로 견고하나 실무 가이드 강화 필요

## 상세 분석 결과
### 1. Foundation - Trust (75/100)
- 강점: YAML 구조 완벽, TRUST 5원칙 명확
- 약점: 검증 명령어 부족, 예시 추상적

### 2. Alfred - Tag-scanning (68/100)
- 강점: CODE-FIRST 원칙 명확
- 약점: 템플릿 파일 누락, 알고리즘 미설명

... (계속)

## Cross-Tier 패턴
- 공통 강점: 메타데이터 표준화, 문서 체계
- 공통 약점: 코드 예시 부족, 워크플로우 미흡

## 액션 플랜
Phase 1 (1주일): 긴급 - 템플릿, 명령어, 코드 예시
Phase 2 (2주일): 진행 - 사용 사례, 보안, 스크립트
Phase 3 (1개월): 지속 - 에러 처리, CI/CD, 테스트
```

### 파일 생성
```bash
파일: .claude/skills/moai-skill-factory/PARALLEL-ANALYSIS-REPORT.md
크기: 약 600-800줄
내용: 종합 분석, 계층별 평가, 개선사항, 액션 플랜
```

---

## 🔄 실제 실행 예시

### 커맨드라인 실행 (의사코드)
```bash
# Step 1: 스킬 폴더 확인
$ ls -la .claude/skills/ | grep moai-

# Step 2: 4개 에이전트 병렬 실행
# (Claude Code에서 동시에 호출)

# Step 3: 결과 수집
Agent 1: ✅ Foundation Trust 완료 (JSON)
Agent 2: ✅ Alfred Tag-scanning 완료 (JSON)
Agent 3: ✅ Domain Backend 완료 (JSON)
Agent 4: ✅ Language Python 완료 (JSON)

# Step 4: 보고서 생성
$ echo "병렬 분석 결과 통합 중..."
$ cat > PARALLEL-ANALYSIS-REPORT.md << EOF
# 분석 보고서
(4개 분석 결과 통합)
EOF
```

---

## 📊 분석 메트릭 (이해하기)

### 완성도 점수 해석
```
90-100: ⭐⭐⭐ 모범 사례 (production-ready)
80-89:  ⭐⭐   우수 (경미한 개선 필요)
70-79:  ⭐    개선 필요 (주요 내용 있으나 미흡)
60-69:  ⚠️    미완성 (구조만 있고 내용 부족)
<60:    🔴    불완전 (재작업 필요)
```

### 점수 구성 요소
```
메타데이터:     20% (구조 기초)
문서 구조:      15% (표준 준수)
내용 깊이:      25% (개념 설명)
코드 예시:      20% (실전성)
워크플로우:     10% (사용 가이드)
에러 처리:      10% (복구 절차)
────────────────────
합계:          100%
```

---

## 🎓 핵심 학습 포인트

### 병렬 분석의 강점
1. **시간 효율**: 순차 60분 → 병렬 15분 (4배 개선)
2. **일관성**: 동일 기준으로 동시 평가
3. **패턴 발견**: Cross-Tier 비교로 공통 약점 도출
4. **우선순위**: 영향도 분석으로 ROI 최적화

### Skill-factory 에이전트의 역할
- YAML 구조 검증
- 문서 표준 준수도 평가
- 완성도 점수 객관적 산출
- 개선사항 구체화 및 우선순위 지정

### 개선 권장사항의 실행 방식
1. **코드 예시**: 각 언어별 최소 3개씩
2. **템플릿**: pyproject.toml, pytest.ini 등 참조 제공
3. **워크플로우**: RED→GREEN→REFACTOR TDD 사이클
4. **에러 처리**: 공통 에러 패턴 카탈로그화

---

## ✅ 완료 체크리스트

- [ ] Step 1: 분석 대상 4개 스킬 선정 완료
- [ ] Step 2: 4개 에이전트 병렬 실행 완료
- [ ] Step 3: 결과 통합 및 보고서 작성 완료
- [ ] 액션 플랜 Phase 1 검토 완료
- [ ] 개선사항 우선순위 확인 완료

---

**다음 단계**: PARALLEL-ANALYSIS-REPORT.md 검토 → Phase 1 액션 플랜 실행

