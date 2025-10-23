# Implementation Plan: SPEC-PROJECT-001

> **SPEC ID**: PROJECT-001
> **Title**: Gradle 멀티 모듈 프로젝트 초기 세팅 (CQRS 기반 도메인 분리)
> **Version**: 0.0.1
> **Status**: draft

---

## 개요

본 계획서는 SPEC-PROJECT-001의 구현 전략을 정의합니다. CQRS 패턴 기반의 도메인 중심 멀티 모듈 구조를 점진적으로 구축하며, 초기에는 common과 product 모듈만 생성합니다.

---

## 구현 우선순위

### 🔴 Critical Priority: 루트 프로젝트 설정
- 모든 후속 작업의 기반이 되는 루트 프로젝트 구성
- Gradle 멀티 모듈 구조의 뼈대 확립

### 🟠 High Priority: common 모듈 생성
- 모든 도메인 모듈이 의존하는 공통 기반 제공
- 공통 예외, 응답 포맷, 유틸리티의 향후 확장 준비

### 🟡 Medium Priority: product 모듈 생성
- CQRS 패턴의 실제 적용 사례
- 향후 다른 도메인 모듈의 템플릿 역할

### 🟢 Low Priority: 빌드 검증 및 문서화
- 모듈 간 의존성 무결성 확인
- 아키텍처 문서 작성

---

## Phase 1: 루트 프로젝트 설정

### 목표
Gradle 멀티 모듈 프로젝트의 기반을 구축하고, 공통 의존성 및 플러그인을 설정합니다.

### 작업 항목

#### 1.1 settings.gradle.kts 생성
- [ ] 루트 프로젝트 이름 설정: `rootProject.name = "SDD-vibe-2"`
- [ ] 서브 모듈 포함 선언:
  ```kotlin
  include("common")
  include("product")
  ```
- [ ] @CODE:PROJECT-001:CONFIG TAG 주석 추가

#### 1.2 루트 build.gradle.kts 생성
- [ ] 플러그인 설정:
  - `java`
  - `org.springframework.boot` (version 3.2.0, apply false)
  - `io.spring.dependency-management` (version 1.1.4, apply false)
- [ ] allprojects 블록:
  - group: `com.sdd.vibe`
  - version: `0.4.10`
  - repository: `mavenCentral()`
- [ ] subprojects 블록:
  - Java 17 sourceCompatibility 설정
  - 공통 의존성 선언 (Spring Boot Starter, JUnit 5, Mockito)
  - Test task 설정: `useJUnitPlatform()`
- [ ] @CODE:PROJECT-001:CONFIG TAG 주석 추가

#### 1.3 .gitignore 업데이트
- [ ] Gradle 빌드 산출물 제외:
  ```
  .gradle/
  build/
  **/build/
  !gradle-wrapper.jar
  ```
- [ ] IDE 관련 파일 제외:
  ```
  .idea/
  *.iml
  ```

### 검증 기준
- ✅ `./gradlew projects` 실행 시 루트 프로젝트 출력
- ✅ 루트 build.gradle.kts에 공통 의존성 정의 완료
- ✅ Git 상태 확인: 불필요한 빌드 산출물이 추적되지 않음

---

## Phase 2: common 모듈 생성

### 목표
모든 도메인 모듈이 의존할 공통 기반 모듈을 생성합니다.

### 작업 항목

#### 2.1 디렉토리 구조 생성
- [ ] `common/` 디렉토리 생성
- [ ] 소스 디렉토리 생성:
  ```
  common/src/main/java/com/sdd/vibe/common/
  common/src/main/java/com/sdd/vibe/common/exception/
  common/src/main/java/com/sdd/vibe/common/response/
  common/src/main/java/com/sdd/vibe/common/util/
  ```
- [ ] 테스트 디렉토리 생성:
  ```
  common/src/test/java/com/sdd/vibe/common/
  ```
- [ ] @CODE:PROJECT-001:STRUCTURE TAG 주석 추가 (README 파일)

#### 2.2 common/build.gradle.kts 작성
- [ ] 공통 의존성 선언:
  - `spring-boot-starter-web`
  - `spring-boot-starter-validation`
- [ ] @CODE:PROJECT-001:CONFIG TAG 주석 추가
- [ ] 주석으로 향후 추가 예정 항목 명시:
  ```kotlin
  // TODO: 공통 예외 클래스 추가 예정
  // TODO: 공통 응답 포맷 추가 예정
  // TODO: 공통 유틸리티 추가 예정
  ```

#### 2.3 settings.gradle.kts 업데이트
- [ ] `include("common")` 이미 포함되었는지 확인

### 검증 기준
- ✅ `./gradlew :common:build` 실행 시 빌드 성공
- ✅ `common/build.gradle.kts`에 공통 의존성 정의 완료
- ✅ 디렉토리 구조가 설계대로 생성됨

---

## Phase 3: product 모듈 생성 (CQRS 구조)

### 목표
CQRS 패턴을 적용한 첫 번째 도메인 모듈을 생성하고, 향후 다른 도메인 모듈의 템플릿으로 활용합니다.

### 작업 항목

#### 3.1 디렉토리 구조 생성 (CQRS 분리)
- [ ] `product/` 디렉토리 생성
- [ ] Command 측 소스 디렉토리:
  ```
  product/src/main/java/com/sdd/vibe/product/command/
  product/src/main/java/com/sdd/vibe/product/command/controller/
  product/src/main/java/com/sdd/vibe/product/command/service/
  product/src/main/java/com/sdd/vibe/product/command/domain/
  product/src/main/java/com/sdd/vibe/product/command/repository/
  ```
- [ ] Query 측 소스 디렉토리:
  ```
  product/src/main/java/com/sdd/vibe/product/query/
  product/src/main/java/com/sdd/vibe/product/query/controller/
  product/src/main/java/com/sdd/vibe/product/query/service/
  product/src/main/java/com/sdd/vibe/product/query/dto/
  product/src/main/java/com/sdd/vibe/product/query/repository/
  ```
- [ ] 테스트 디렉토리:
  ```
  product/src/test/java/com/sdd/vibe/product/command/
  product/src/test/java/com/sdd/vibe/product/query/
  ```
- [ ] @CODE:PROJECT-001:STRUCTURE TAG 주석 추가 (README 파일)

#### 3.2 product/build.gradle.kts 작성
- [ ] common 모듈 의존성 추가:
  ```kotlin
  dependencies {
      implementation(project(":common"))
      implementation("org.springframework.boot:spring-boot-starter-data-jpa")
      runtimeOnly("com.h2database:h2")
  }
  ```
- [ ] @CODE:PROJECT-001:CONFIG TAG 주석 추가

#### 3.3 settings.gradle.kts 업데이트
- [ ] `include("product")` 이미 포함되었는지 확인

#### 3.4 CQRS 패턴 설명 주석 추가
- [ ] command 패키지에 README 또는 package-info.java 생성:
  ```java
  /**
   * Command 패키지: 쓰기 작업 (등록, 수정, 삭제)
   * - Controller: Command API 엔드포인트
   * - Service: 비즈니스 로직 처리
   * - Domain: Entity, Aggregate, Value Object
   * - Repository: 쓰기 전용 Repository
   *
   * @TAG @CODE:PROJECT-001:STRUCTURE
   */
  ```
- [ ] query 패키지에 README 또는 package-info.java 생성:
  ```java
  /**
   * Query 패키지: 읽기 작업 (조회, 검색)
   * - Controller: Query API 엔드포인트
   * - Service: 조회 로직 처리
   * - DTO: 응답 DTO, Projection
   * - Repository: 읽기 전용 Repository
   *
   * @TAG @CODE:PROJECT-001:STRUCTURE
   */
  ```

### 검증 기준
- ✅ `./gradlew :product:build` 실행 시 빌드 성공
- ✅ command/query 디렉토리가 설계대로 분리됨
- ✅ `product/build.gradle.kts`에 `implementation(project(":common"))` 선언됨
- ✅ 테스트 디렉토리가 command/query로 분리됨

---

## Phase 4: 빌드 검증 및 문서화

### 목표
전체 멀티 모듈 프로젝트의 빌드 무결성을 검증하고, 아키텍처 문서를 작성합니다.

### 작업 항목

#### 4.1 전체 빌드 검증
- [ ] `./gradlew clean build` 실행하여 전체 빌드 성공 확인
- [ ] 각 모듈별 독립 빌드 테스트:
  - `./gradlew :common:build`
  - `./gradlew :product:build`
- [ ] 빌드 산출물 확인:
  - `common/build/libs/common-0.4.10.jar`
  - `product/build/libs/product-0.4.10.jar`

#### 4.2 순환 참조 검증
- [ ] Gradle 빌드 로그에서 순환 참조 경고 확인
- [ ] 모듈 간 의존성 그래프 출력:
  ```bash
  ./gradlew :product:dependencies --configuration compileClasspath
  ```
- [ ] 기대 결과: `product → common` 의존성만 존재, 순환 참조 없음

#### 4.3 TAG 체인 검증
- [ ] SPEC TAG 존재 확인:
  ```bash
  rg "@SPEC:PROJECT-001" -n .moai/specs/
  ```
- [ ] CODE TAG 존재 확인:
  ```bash
  rg "@CODE:PROJECT-001" -n settings.gradle.kts build.gradle.kts common/ product/
  ```
- [ ] 전체 TAG 체인 검증:
  ```bash
  rg "@(SPEC|CODE|TEST|DOC):PROJECT-001" -n
  ```

#### 4.4 아키텍처 문서 작성 (추후)
- [ ] `docs/architecture/multi-module.md` 생성 (향후 작업)
- [ ] 멀티 모듈 구조 다이어그램 추가
- [ ] CQRS 패턴 적용 가이드 작성
- [ ] @DOC:PROJECT-001 TAG 추가

### 검증 기준
- ✅ `./gradlew build` 실행 시 전체 빌드 성공
- ✅ 순환 참조 에러 없음
- ✅ 각 모듈의 jar 파일이 `build/libs/`에 생성됨
- ✅ TAG 체인이 완전히 연결됨 (SPEC → CODE)

---

## 기술적 접근 방법

### Gradle 멀티 모듈 설정 전략

#### 의존성 관리
- **루트 프로젝트**: 공통 플러그인 및 의존성 버전 관리
- **subprojects 블록**: 모든 서브 모듈에 공통 설정 적용
- **각 모듈**: 도메인 특화 의존성만 추가

#### 모듈 간 의존성 규칙
```
product → common (허용)
common → product (금지, 순환 참조)
product → order (금지, 도메인 간 직접 의존)
```

### CQRS 패턴 구조화

#### Command 측 (쓰기 작업)
- **책임**: 상태 변경 (등록, 수정, 삭제)
- **트랜잭션**: 항상 트랜잭션 경계 내에서 실행
- **반환값**: 성공/실패 여부, 생성된 ID

#### Query 측 (읽기 작업)
- **책임**: 데이터 조회 (단건 조회, 목록 조회, 검색)
- **최적화**: 읽기 전용 쿼리 최적화, Projection 활용
- **반환값**: DTO, Pageable 결과

---

## 리스크 및 대응 전략

### 🚨 Risk 1: 순환 참조 발생 가능성
**완화 전략**:
- common 모듈은 어떤 도메인 모듈에도 의존하지 않도록 엄격히 제한
- 빌드 시 순환 참조 자동 감지 활성화
- 코드 리뷰에서 의존성 방향 검증

### 🚨 Risk 2: 모듈 간 중복 코드 증가
**완화 전략**:
- 공통 코드는 common 모듈로 적극 이동
- 각 도메인의 특화 로직은 중복을 허용 (독립성 우선)
- 정기적인 리팩토링으로 중복 최소화

### 🚨 Risk 3: CQRS 패턴 복잡도 증가
**완화 전략**:
- 초기에는 단순한 CQRS 적용 (Command/Query 분리만)
- Event Sourcing, Saga 등 고급 패턴은 필요 시점에 도입
- 명확한 패키지 구조와 네이밍 컨벤션 유지

---

## 아키�ecture 설계 방향

### 모듈 확장 계획
```
현재 (v0.0.1):
- common
- product

향후 확장:
- order      (주문 도메인, SPEC-ORDER-001)
- payment    (결제 도메인, SPEC-PAYMENT-001)
- user       (사용자 도메인, SPEC-USER-001)
- inventory  (재고 도메인, SPEC-INVENTORY-001)
```

### 모듈 간 통신 전략
- **초기**: 모듈 간 직접 의존성 (Gradle project dependency)
- **향후**: 이벤트 기반 통신 고려 (Spring Event, Kafka)

### 공통 모듈 확장 방향
```
common/
├── exception/      # 공통 예외 (BusinessException, ErrorCode)
├── response/       # 공통 응답 (ApiResponse<T>, PageResponse<T>)
├── util/           # 공통 유틸리티 (DateUtil, StringUtil)
└── config/         # 공통 설정 (JPA, Security, etc.)
```

---

## Definition of Done (완료 조건)

### Phase 1 완료 기준
- [x] settings.gradle.kts 생성 완료
- [x] 루트 build.gradle.kts 생성 완료
- [x] .gitignore 업데이트 완료
- [x] `./gradlew projects` 실행 성공

### Phase 2 완료 기준
- [x] common 모듈 디렉토리 구조 생성 완료
- [x] common/build.gradle.kts 작성 완료
- [x] `./gradlew :common:build` 실행 성공

### Phase 3 완료 기준
- [x] product 모듈 CQRS 디렉토리 구조 생성 완료
- [x] product/build.gradle.kts 작성 완료 (common 의존성 포함)
- [x] `./gradlew :product:build` 실행 성공

### Phase 4 완료 기준
- [x] `./gradlew build` 전체 빌드 성공
- [x] 순환 참조 검증 완료
- [x] TAG 체인 검증 완료
- [x] 각 모듈의 jar 파일 생성 확인

### 전체 SPEC 완료 기준
- [x] 모든 Phase 완료
- [x] acceptance.md의 모든 AC 통과
- [x] Living Document 업데이트 완료 (`/alfred:3-sync`)
- [x] Git 브랜치 전략에 따라 PR 생성 완료

---

## 다음 단계 제안

### 즉시 수행
1. **SPEC 승인**: suzhanlee 님의 SPEC 검토 및 승인
2. **구현 시작**: `/alfred:2-run SPEC-PROJECT-001` 실행

### 구현 완료 후
1. **문서 동기화**: `/alfred:3-sync` 실행
2. **TAG 체인 검증**: tag-agent로 전체 TAG 무결성 확인
3. **Git 작업**: git-manager를 통한 PR 생성

### 후속 SPEC 작성
1. **SPEC-PRODUCT-001**: 상품 등록 기능 (Command)
2. **SPEC-PRODUCT-002**: 상품 조회 기능 (Query)
3. **SPEC-ORDER-001**: 주문 처리 기능

---

**작성자**: @agent-spec-builder
**작성일**: 2025-10-23
**문서 버전**: 0.0.1
