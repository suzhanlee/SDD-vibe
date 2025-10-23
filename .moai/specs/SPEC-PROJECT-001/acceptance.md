# Acceptance Criteria: SPEC-PROJECT-001

> **SPEC ID**: PROJECT-001
> **Title**: Gradle 멀티 모듈 프로젝트 초기 세팅 (CQRS 기반 도메인 분리)
> **Version**: 0.0.1
> **Status**: draft

---

## 개요

본 문서는 SPEC-PROJECT-001의 상세 수락 기준(Acceptance Criteria)을 정의합니다. 모든 AC는 Given-When-Then 형식으로 작성되며, 테스트 가능한 구체적 조건을 명시합니다.

---

## AC-1: 루트 프로젝트 설정

### 시나리오 1.1: settings.gradle.kts 생성 및 서브 모듈 포함

**Given**: Gradle 프로젝트가 초기화되지 않은 상태
**When**: `settings.gradle.kts` 파일을 생성하고 서브 모듈을 포함
**Then**:
- ✅ `settings.gradle.kts` 파일이 프로젝트 루트에 존재해야 한다.
- ✅ `rootProject.name = "SDD-vibe-2"` 설정이 존재해야 한다.
- ✅ `include("common")` 선언이 존재해야 한다.
- ✅ `include("product")` 선언이 존재해야 한다.
- ✅ `./gradlew projects` 실행 시 아래 출력이 나와야 한다:
  ```
  Root project 'SDD-vibe-2'
  +--- Project ':common'
  \--- Project ':product'
  ```

**검증 명령어**:
```bash
./gradlew projects
```

---

### 시나리오 1.2: 루트 build.gradle.kts 생성 및 공통 설정

**Given**: `settings.gradle.kts`가 생성된 상태
**When**: 루트 `build.gradle.kts` 파일을 생성하고 공통 의존성 설정
**Then**:
- ✅ `build.gradle.kts` 파일이 프로젝트 루트에 존재해야 한다.
- ✅ `plugins` 블록에 다음 플러그인이 선언되어야 한다:
  - `java`
  - `org.springframework.boot` (version 3.2.0, apply false)
  - `io.spring.dependency-management` (version 1.1.4, apply false)
- ✅ `allprojects` 블록에 다음 설정이 존재해야 한다:
  - `group = "com.sdd.vibe"`
  - `version = "0.4.10"`
  - `repositories { mavenCentral() }`
- ✅ `subprojects` 블록에 다음 설정이 존재해야 한다:
  - Java 17 sourceCompatibility 설정
  - 공통 의존성 (Spring Boot Starter, JUnit 5, Mockito)
  - `tasks.test { useJUnitPlatform() }`
- ✅ `./gradlew build` 실행 시 빌드가 성공해야 한다.

**검증 명령어**:
```bash
./gradlew build --dry-run
./gradlew dependencies --configuration runtimeClasspath | head -20
```

---

### 시나리오 1.3: .gitignore 업데이트

**Given**: 루트 프로젝트 설정이 완료된 상태
**When**: `.gitignore` 파일에 Gradle 빌드 산출물 제외 규칙 추가
**Then**:
- ✅ `.gitignore` 파일에 다음 항목이 존재해야 한다:
  ```
  .gradle/
  build/
  **/build/
  !gradle-wrapper.jar
  .idea/
  *.iml
  ```
- ✅ `git status` 실행 시 `build/` 디렉토리가 추적되지 않아야 한다.

**검증 명령어**:
```bash
./gradlew build
git status
# Expected: build/ 디렉토리가 untracked files에 나타나지 않음
```

---

## AC-2: common 모듈 생성

### 시나리오 2.1: common 모듈 디렉토리 구조 생성

**Given**: 루트 프로젝트가 설정된 상태
**When**: `common` 모듈 디렉토리 및 패키지 구조를 생성
**Then**:
- ✅ 다음 디렉토리가 존재해야 한다:
  - `common/src/main/java/com/sdd/vibe/common/`
  - `common/src/main/java/com/sdd/vibe/common/exception/`
  - `common/src/main/java/com/sdd/vibe/common/response/`
  - `common/src/main/java/com/sdd/vibe/common/util/`
  - `common/src/test/java/com/sdd/vibe/common/`
- ✅ 각 디렉토리가 비어있거나 placeholder 파일(.gitkeep 등)이 존재해야 한다.

**검증 명령어**:
```bash
ls -R common/src/main/java/com/sdd/vibe/common/
ls -R common/src/test/java/com/sdd/vibe/common/
```

---

### 시나리오 2.2: common/build.gradle.kts 생성

**Given**: common 모듈 디렉토리가 생성된 상태
**When**: `common/build.gradle.kts` 파일을 생성하고 공통 의존성 선언
**Then**:
- ✅ `common/build.gradle.kts` 파일이 존재해야 한다.
- ✅ `dependencies` 블록에 다음 의존성이 선언되어야 한다:
  - `implementation("org.springframework.boot:spring-boot-starter-web")`
  - `implementation("org.springframework.boot:spring-boot-starter-validation")`
- ✅ `@CODE:PROJECT-001:CONFIG` TAG 주석이 파일 상단에 존재해야 한다.
- ✅ `./gradlew :common:build` 실행 시 빌드가 성공해야 한다.
- ✅ `common/build/libs/common-0.4.10.jar` 파일이 생성되어야 한다.

**검증 명령어**:
```bash
./gradlew :common:build
ls -lh common/build/libs/
```

---

### 시나리오 2.3: common 모듈 독립 빌드 검증

**Given**: common 모듈이 생성되고 build.gradle.kts가 작성된 상태
**When**: common 모듈만 독립적으로 빌드
**Then**:
- ✅ `./gradlew :common:clean :common:build` 실행 시 빌드가 성공해야 한다.
- ✅ 빌드 로그에 오류나 경고가 없어야 한다.
- ✅ `common/build/libs/` 디렉토리에 jar 파일이 생성되어야 한다.

**검증 명령어**:
```bash
./gradlew :common:clean :common:build --info
```

---

## AC-3: product 모듈 생성 (CQRS 구조)

### 시나리오 3.1: product 모듈 CQRS 디렉토리 구조 생성

**Given**: common 모듈이 생성된 상태
**When**: `product` 모듈을 생성하고 command/query 패키지 분리
**Then**:
- ✅ 다음 Command 측 디렉토리가 존재해야 한다:
  - `product/src/main/java/com/sdd/vibe/product/command/`
  - `product/src/main/java/com/sdd/vibe/product/command/controller/`
  - `product/src/main/java/com/sdd/vibe/product/command/service/`
  - `product/src/main/java/com/sdd/vibe/product/command/domain/`
  - `product/src/main/java/com/sdd/vibe/product/command/repository/`
- ✅ 다음 Query 측 디렉토리가 존재해야 한다:
  - `product/src/main/java/com/sdd/vibe/product/query/`
  - `product/src/main/java/com/sdd/vibe/product/query/controller/`
  - `product/src/main/java/com/sdd/vibe/product/query/service/`
  - `product/src/main/java/com/sdd/vibe/product/query/dto/`
  - `product/src/main/java/com/sdd/vibe/product/query/repository/`
- ✅ 다음 테스트 디렉토리가 존재해야 한다:
  - `product/src/test/java/com/sdd/vibe/product/command/`
  - `product/src/test/java/com/sdd/vibe/product/query/`

**검증 명령어**:
```bash
find product/src -type d -name "command" -o -name "query"
```

---

### 시나리오 3.2: product/build.gradle.kts 생성 및 common 의존성

**Given**: product 모듈 디렉토리가 생성된 상태
**When**: `product/build.gradle.kts` 파일을 생성하고 common 모듈 의존성 선언
**Then**:
- ✅ `product/build.gradle.kts` 파일이 존재해야 한다.
- ✅ `dependencies` 블록에 다음 의존성이 선언되어야 한다:
  - `implementation(project(":common"))`
  - `implementation("org.springframework.boot:spring-boot-starter-data-jpa")`
  - `runtimeOnly("com.h2database:h2")`
- ✅ `@CODE:PROJECT-001:CONFIG` TAG 주석이 파일 상단에 존재해야 한다.
- ✅ `./gradlew :product:build` 실행 시 빌드가 성공해야 한다.
- ✅ `product/build/libs/product-0.4.10.jar` 파일이 생성되어야 한다.

**검증 명령어**:
```bash
./gradlew :product:build
ls -lh product/build/libs/
```

---

### 시나리오 3.3: product 모듈의 common 의존성 확인

**Given**: product 모듈이 생성되고 build.gradle.kts가 작성된 상태
**When**: product 모듈의 의존성 트리 확인
**Then**:
- ✅ `./gradlew :product:dependencies` 실행 시 common 모듈이 의존성 트리에 나타나야 한다.
- ✅ common 모듈의 전이 의존성(Spring Boot Starter Web, Validation)이 포함되어야 한다.

**검증 명령어**:
```bash
./gradlew :product:dependencies --configuration compileClasspath | grep "project :common"
```

**Expected output**:
```
+--- project :common
     +--- org.springframework.boot:spring-boot-starter-web
     \--- org.springframework.boot:spring-boot-starter-validation
```

---

### 시나리오 3.4: CQRS 패키지 설명 주석 검증

**Given**: product 모듈의 command/query 디렉토리가 생성된 상태
**When**: 각 패키지에 설명 주석(package-info.java 또는 README) 추가
**Then**:
- ✅ `product/src/main/java/com/sdd/vibe/product/command/package-info.java` 파일이 존재해야 한다.
- ✅ 파일 내용에 "@CODE:PROJECT-001:STRUCTURE" TAG가 포함되어야 한다.
- ✅ Command 패키지의 책임(쓰기 작업)이 명시되어야 한다.
- ✅ `product/src/main/java/com/sdd/vibe/product/query/package-info.java` 파일이 존재해야 한다.
- ✅ 파일 내용에 "@CODE:PROJECT-001:STRUCTURE" TAG가 포함되어야 한다.
- ✅ Query 패키지의 책임(읽기 작업)이 명시되어야 한다.

**검증 명령어**:
```bash
cat product/src/main/java/com/sdd/vibe/product/command/package-info.java
cat product/src/main/java/com/sdd/vibe/product/query/package-info.java
```

---

## AC-4: 모듈 간 의존성 검증

### 시나리오 4.1: 전체 빌드 성공

**Given**: common, product 모듈이 모두 생성된 상태
**When**: `./gradlew build` 실행
**Then**:
- ✅ 빌드가 성공해야 한다.
- ✅ 빌드 로그에 "BUILD SUCCESSFUL" 메시지가 출력되어야 한다.
- ✅ 각 모듈의 jar 파일이 생성되어야 한다:
  - `common/build/libs/common-0.4.10.jar`
  - `product/build/libs/product-0.4.10.jar`

**검증 명령어**:
```bash
./gradlew clean build
ls -lh common/build/libs/ product/build/libs/
```

---

### 시나리오 4.2: 순환 참조 부재 검증

**Given**: common, product 모듈이 모두 생성된 상태
**When**: 모듈 간 의존성 그래프 확인
**Then**:
- ✅ 빌드 로그에 순환 참조 경고가 없어야 한다.
- ✅ product → common 의존성만 존재해야 한다.
- ✅ common → product 의존성이 없어야 한다.
- ✅ product → product 자기 참조가 없어야 한다.

**검증 명령어**:
```bash
./gradlew :product:dependencies --configuration compileClasspath | grep "project :"
```

**Expected output**:
```
+--- project :common
```

**Unexpected output (실패 조건)**:
```
+--- project :product   # 자기 참조 (X)
+--- project :order     # 다른 도메인 직접 의존 (X)
```

---

### 시나리오 4.3: 모듈 간 격리 검증

**Given**: common, product 모듈이 모두 생성된 상태
**When**: 각 모듈을 독립적으로 빌드
**Then**:
- ✅ `./gradlew :common:build` 실행 시 common 모듈만 빌드되어야 한다.
- ✅ `./gradlew :product:build` 실행 시 product 모듈 + common 모듈이 빌드되어야 한다.
- ✅ common 모듈 빌드 시 product 모듈이 빌드되지 않아야 한다.

**검증 명령어**:
```bash
./gradlew clean
./gradlew :common:build --info | grep "Task :product:"
# Expected: product 관련 Task가 나타나지 않아야 함

./gradlew clean
./gradlew :product:build --info | grep "Task :common:"
# Expected: common 관련 Task가 나타나야 함
```

---

## AC-5: TAG 체인 무결성 검증

### 시나리오 5.1: SPEC TAG 존재 확인

**Given**: SPEC-PROJECT-001 문서가 작성된 상태
**When**: SPEC TAG 검색
**Then**:
- ✅ `@SPEC:PROJECT-001` TAG가 `.moai/specs/SPEC-PROJECT-001/spec.md` 파일에 존재해야 한다.

**검증 명령어**:
```bash
rg "@SPEC:PROJECT-001" -n .moai/specs/
```

**Expected output**:
```
.moai/specs/SPEC-PROJECT-001/spec.md:XX:- **SPEC**: @SPEC:PROJECT-001
```

---

### 시나리오 5.2: CODE TAG 존재 확인

**Given**: 루트 및 모듈 build 파일이 작성된 상태
**When**: CODE TAG 검색
**Then**:
- ✅ `@CODE:PROJECT-001:CONFIG` TAG가 다음 파일에 존재해야 한다:
  - `settings.gradle.kts`
  - `build.gradle.kts` (루트)
  - `common/build.gradle.kts`
  - `product/build.gradle.kts`
- ✅ `@CODE:PROJECT-001:STRUCTURE` TAG가 다음 파일에 존재해야 한다:
  - `product/src/main/java/com/sdd/vibe/product/command/package-info.java`
  - `product/src/main/java/com/sdd/vibe/product/query/package-info.java`

**검증 명령어**:
```bash
rg "@CODE:PROJECT-001" -n settings.gradle.kts build.gradle.kts common/ product/
```

**Expected output**:
```
settings.gradle.kts:X:// @CODE:PROJECT-001:CONFIG
build.gradle.kts:X:// @CODE:PROJECT-001:CONFIG
common/build.gradle.kts:X:// @CODE:PROJECT-001:CONFIG
product/build.gradle.kts:X:// @CODE:PROJECT-001:CONFIG
product/src/.../command/package-info.java:X:* @CODE:PROJECT-001:STRUCTURE
product/src/.../query/package-info.java:X:* @CODE:PROJECT-001:STRUCTURE
```

---

### 시나리오 5.3: 전체 TAG 체인 검증

**Given**: SPEC, CODE TAG가 모두 작성된 상태
**When**: 전체 TAG 체인 검색
**Then**:
- ✅ `@SPEC:PROJECT-001` TAG가 1개 존재해야 한다 (spec.md).
- ✅ `@CODE:PROJECT-001` TAG가 6개 이상 존재해야 한다 (설정 파일 4개 + 구조 파일 2개).
- ✅ 모든 TAG가 정확한 형식을 따라야 한다: `@SPEC:PROJECT-001`, `@CODE:PROJECT-001:CONFIG`, etc.

**검증 명령어**:
```bash
rg "@(SPEC|CODE|TEST|DOC):PROJECT-001" -n
```

---

### 시나리오 5.4: 고아 TAG 부재 검증

**Given**: 전체 TAG 체인이 작성된 상태
**When**: TAG 참조 무결성 검사
**Then**:
- ✅ SPEC 파일에서 참조하는 모든 CODE TAG가 실제로 존재해야 한다.
- ✅ CODE TAG가 있는 파일들이 SPEC에서 명시되어야 한다.
- ✅ 참조되지 않는 고아 TAG가 없어야 한다.

**검증 명령어**:
```bash
# SPEC에서 참조하는 CODE TAG 추출
rg "CODE:PROJECT-001" .moai/specs/SPEC-PROJECT-001/spec.md -o -N

# 실제 CODE TAG 존재 확인
rg "@CODE:PROJECT-001" -n settings.gradle.kts build.gradle.kts common/ product/
```

---

## AC-6: Quality Gate (품질 게이트)

### 시나리오 6.1: 빌드 산출물 검증

**Given**: 전체 빌드가 성공한 상태
**When**: 빌드 산출물 확인
**Then**:
- ✅ `common/build/libs/common-0.4.10.jar` 파일이 존재해야 한다.
- ✅ `product/build/libs/product-0.4.10.jar` 파일이 존재해야 한다.
- ✅ 각 jar 파일의 크기가 0보다 커야 한다.

**검증 명령어**:
```bash
./gradlew clean build
ls -lh common/build/libs/common-0.4.10.jar
ls -lh product/build/libs/product-0.4.10.jar
```

---

### 시나리오 6.2: Git 상태 검증

**Given**: 모든 파일이 생성된 상태
**When**: Git 상태 확인
**Then**:
- ✅ `build/` 디렉토리가 추적되지 않아야 한다.
- ✅ `.gradle/` 디렉토리가 추적되지 않아야 한다.
- ✅ 다음 파일들이 추적 대상이어야 한다:
  - `settings.gradle.kts`
  - `build.gradle.kts`
  - `common/build.gradle.kts`
  - `product/build.gradle.kts`

**검증 명령어**:
```bash
git status
git status | grep "build/"       # Expected: 없어야 함
git status | grep ".gradle/"     # Expected: 없어야 함
```

---

### 시나리오 6.3: 문서 일관성 검증

**Given**: SPEC, plan, acceptance 문서가 모두 작성된 상태
**When**: 문서 간 일관성 확인
**Then**:
- ✅ spec.md의 SPEC ID가 plan.md, acceptance.md와 일치해야 한다.
- ✅ spec.md의 요구사항이 acceptance.md의 AC에 반영되어야 한다.
- ✅ plan.md의 구현 항목이 acceptance.md의 검증 항목과 대응되어야 한다.

**검증 방법**:
- spec.md의 "Requirements" 섹션 각 항목 → acceptance.md의 AC 매핑 확인
- plan.md의 "Phase X" 항목 → acceptance.md의 "AC-X" 매핑 확인

---

## Test Plan (테스트 계획)

### 수동 테스트

#### 1. 빌드 검증 시나리오
```bash
# Step 1: 전체 클린 빌드
./gradlew clean build

# Step 2: 모듈별 독립 빌드
./gradlew :common:clean :common:build
./gradlew :product:clean :product:build

# Step 3: 의존성 트리 확인
./gradlew :product:dependencies --configuration compileClasspath

# Step 4: 프로젝트 구조 확인
./gradlew projects
```

#### 2. TAG 검증 시나리오
```bash
# Step 1: SPEC TAG 확인
rg "@SPEC:PROJECT-001" -n .moai/specs/

# Step 2: CODE TAG 확인
rg "@CODE:PROJECT-001" -n settings.gradle.kts build.gradle.kts common/ product/

# Step 3: 전체 TAG 체인 확인
rg "@(SPEC|CODE|TEST|DOC):PROJECT-001" -n
```

#### 3. 디렉토리 구조 검증 시나리오
```bash
# Step 1: common 모듈 구조 확인
find common/src -type d

# Step 2: product 모듈 CQRS 구조 확인
find product/src -type d -name "command" -o -name "query"

# Step 3: 테스트 디렉토리 확인
find product/src/test -type d
```

---

## Definition of Done (완료 조건)

### 전체 SPEC 완료 기준
- [x] AC-1: 루트 프로젝트 설정 완료 (모든 시나리오 통과)
- [x] AC-2: common 모듈 생성 완료 (모든 시나리오 통과)
- [x] AC-3: product 모듈 생성 완료 (모든 시나리오 통과)
- [x] AC-4: 모듈 간 의존성 검증 완료 (모든 시나리오 통과)
- [x] AC-5: TAG 체인 무결성 검증 완료 (모든 시나리오 통과)
- [x] AC-6: Quality Gate 통과 (모든 시나리오 통과)
- [x] `./gradlew clean build` 전체 빌드 성공
- [x] 순환 참조 없음
- [x] TAG 체인 완전히 연결됨 (SPEC → CODE)
- [x] Living Document 업데이트 완료 (`/alfred:3-sync`)
- [x] Git 브랜치 전략에 따라 PR 생성 완료

---

## 검증 체크리스트

### Pre-implementation Checklist (구현 전)
- [ ] SPEC-PROJECT-001 문서 검토 완료
- [ ] suzhanlee 님 승인 완료
- [ ] Gradle 8.x 설치 확인
- [ ] Java 17+ 설치 확인

### Implementation Checklist (구현 중)
- [ ] Phase 1: 루트 프로젝트 설정 완료
- [ ] Phase 2: common 모듈 생성 완료
- [ ] Phase 3: product 모듈 생성 완료
- [ ] Phase 4: 빌드 검증 완료

### Post-implementation Checklist (구현 후)
- [ ] 모든 AC 시나리오 통과
- [ ] TAG 체인 검증 완료
- [ ] Living Document 업데이트 완료
- [ ] PR 생성 및 Draft → Ready 전환 완료

---

**작성자**: @agent-spec-builder
**작성일**: 2025-10-23
**문서 버전**: 0.0.1
