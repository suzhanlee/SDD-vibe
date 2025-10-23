---
id: TECH-001
version: 0.2.0
status: active
created: 2025-10-01
updated: 2025-10-23
author: @suzhanlee
priority: high
---

# SDD-vibe-2 Technology Stack

## HISTORY

### v0.2.0 (2025-10-23)
- **UPDATED**: 실제 프로젝트 기술 스택으로 전면 업데이트
- **AUTHOR**: @suzhanlee
- **SECTIONS**: Java 17 + Spring Boot 3.x + JPA 스택 정의
  - Stack: Java 17, Gradle, Spring Boot 3.x
  - Framework: Spring Data JPA, Spring Web, Spring Security
  - Quality: JUnit 5 + Mockito, JaCoCo, Checkstyle
  - Deploy: 로컬 개발 환경 중심

### v0.1.1 (2025-10-17)
- **UPDATED**: Template version synced (v0.3.8)
- **AUTHOR**: @Alfred
- **SECTIONS**: Metadata standardization (single `author` field, added `priority`)

### v0.1.0 (2025-10-01)
- **INITIAL**: Authored the technology stack document
- **AUTHOR**: @tech-lead
- **SECTIONS**: Stack, Framework, Quality, Security, Deploy

---

## @DOC:STACK-001 Languages & Runtimes

### Primary Language

- **Language**: Java
- **Version Range**: Java 17+ (LTS)
- **Rationale**: Spring Boot 3.x의 Jakarta EE 지원 및 최신 Java 기능 활용
- **Package Manager**: Gradle 8.x

### Multi-Platform Support

| Platform    | Support Level | Validation Tooling      | Key Constraints   |
| ----------- | ------------- | ----------------------- | ----------------- |
| **Windows** | 완전 지원     | IntelliJ IDEA, Gradle   | JDK 17+ 설치 필요 |
| **macOS**   | 완전 지원     | IntelliJ IDEA, Gradle   | JDK 17+ 설치 필요 |
| **Linux**   | 완전 지원     | IntelliJ IDEA, Gradle   | JDK 17+ 설치 필요 |

## @DOC:FRAMEWORK-001 Core Frameworks & Libraries

### 1. Runtime Dependencies

```gradle
dependencies {
    // Spring Boot Starters
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-validation'

    // Database
    runtimeOnly 'com.h2database:h2'  // 개발용
    runtimeOnly 'org.postgresql:postgresql'  // 프로덕션용 (추후)

    // Lombok
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

### 2. Development Tooling

```gradle
dependencies {
    // Testing
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.junit.jupiter:junit-jupiter'
    testImplementation 'org.mockito:mockito-core'
    testImplementation 'org.mockito:mockito-junit-jupiter'

    // Code Quality
    testImplementation 'org.jacoco:org.jacoco.core:0.8.11'

    // Documentation
    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
}
```

### 3. Build System

- **Build Tool**: Gradle 8.x (Kotlin DSL 선호)
- **Packaging**: JAR (Spring Boot executable JAR)
- **Targets**: JVM 17+ 기반 백엔드 애플리케이션
- **Performance Goals**: 전체 빌드 시간 30초 이내

## @DOC:QUALITY-001 Quality Gates & Policies

### Test Coverage

- **Target**: 85% 이상 (TRUST 원칙 준수)
- **Measurement Tool**: JaCoCo (Java Code Coverage)
- **Failure Response**: 커버리지 85% 미만 시 빌드 실패, 개선 후 재시도

### Static Analysis

| Tool           | Role               | Config File       | Failure Handling         |
| -------------- | ------------------ | ----------------- | ------------------------ |
| **Checkstyle** | 코딩 컨벤션 검증   | checkstyle.xml    | 위반 시 경고 (빌드 차단) |
| **SpotBugs**   | 버그 패턴 탐지     | spotbugs.xml      | Critical 발견 시 차단    |
| **JUnit 5**    | 단위/통합 테스트   | junit-platform    | 테스트 실패 시 빌드 차단 |

### Automation Scripts

```bash
# Quality gate pipeline
./gradlew clean test              # 모든 테스트 실행
./gradlew jacocoTestReport        # 커버리지 리포트 생성
./gradlew checkstyleMain          # 코딩 컨벤션 검증
./gradlew build                   # 전체 빌드 및 검증
```

## @DOC:SECURITY-001 Security Policy & Operations

### Secret Management

- **Policy**: application.yml에 민감 정보 저장 금지, 환경 변수 또는 application-local.yml 사용
- **Tooling**: Spring Boot의 `@Value`, `Environment` 활용
- **Verification**: `.gitignore`에 `application-local.yml` 포함 확인

### Dependency Security

```gradle
// Dependency vulnerability scanning
plugins {
    id 'org.owasp.dependencycheck' version '8.4.0'
}

dependencyCheck {
    failBuildOnCVSS = 7.0  // CVSS 7.0 이상 취약점 발견 시 빌드 실패
    suppressionFile = 'dependency-check-suppressions.xml'
}
```

### Logging Policy

- **Log Levels**: DEBUG (개발), INFO (테스트), WARN (프로덕션)
- **Sensitive Data Masking**: 비밀번호, 토큰, 개인정보는 로그에 출력 금지
- **Retention Policy**: 로컬 개발 환경에서는 콘솔 출력만 사용 (파일 저장 X)

## @DOC:DEPLOY-001 Release Channels & Strategy

### 1. Distribution Channels

- **Primary Channel**: 로컬 개발 환경 (학습 프로젝트)
- **Release Procedure**: Git 커밋 → SPEC/TEST/CODE 검증 → 로컬 실행
- **Versioning Policy**: Git 태그 기반 버전 관리 (v0.1.0, v0.2.0, ...)
- **Rollback Strategy**: Git revert 또는 브랜치 전환

### 2. Developer Setup

```bash
# Developer mode setup
git clone https://github.com/suzhanlee/SDD-vibe-2.git
cd SDD-vibe-2

# Gradle wrapper로 빌드
./gradlew clean build

# Spring Boot 실행
./gradlew bootRun

# 테스트 실행
./gradlew test
```

### 3. CI/CD Pipeline (추후 계획)

| Stage       | Objective          | Tooling        | Success Criteria        |
| ----------- | ------------------ | -------------- | ----------------------- |
| **Build**   | 프로젝트 빌드      | Gradle         | 빌드 성공               |
| **Test**    | 테스트 실행        | JUnit 5        | 모든 테스트 통과        |
| **Quality** | 품질 게이트 검증   | JaCoCo, CheckStyle | 커버리지 85%+, 컨벤션 준수 |

## Environment Profiles

### Development (`dev`)

```yaml
# application-dev.yml
spring:
  profiles:
    active: dev
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: create-drop
logging:
  level:
    root: DEBUG
```

### Test (`test`)

```yaml
# application-test.yml
spring:
  profiles:
    active: test
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    hibernate:
      ddl-auto: create-drop
logging:
  level:
    root: INFO
```

### Production (`production` - 추후)

```yaml
# application-prod.yml (추후 계획)
spring:
  profiles:
    active: prod
  datasource:
    url: jdbc:postgresql://localhost:5432/sdd_vibe
  jpa:
    hibernate:
      ddl-auto: validate
logging:
  level:
    root: WARN
```

## @CODE:TECH-DEBT-001 Technical Debt Management

### Current Debt (초기 프로젝트이므로 현재 없음)

1. **Gradle 프로젝트 초기 세팅** – 우선순위: 높음
2. **Spring Boot 기본 구조 생성** – 우선순위: 높음
3. **JPA 엔티티 설계** – 우선순위: 중간 (SPEC 작성 후)

### Remediation Plan

- **Short term (1개월 이내)**:
  - Gradle 프로젝트 초기화 및 의존성 설정
  - Spring Boot 기본 구조 생성 (Application, Controller, Service, Repository)
  - H2 Database 연동 및 테스트

- **Mid term (3개월 이내)**:
  - E-commerce 핵심 도메인 구현 (상품, 주문, 결제, 재고)
  - SPEC 문서 20개 이상 작성
  - 테스트 커버리지 85% 달성

- **Long term (6개월 이상)**:
  - SPEC 문서 50개 이상 작성
  - PostgreSQL 전환 및 프로덕션 환경 구축 (선택)
  - CI/CD 파이프라인 구축 (선택)

## EARS Technical Requirements Guide

### Using EARS for the Stack

Apply EARS patterns when documenting technical decisions and quality gates:

#### Technology Stack EARS Example
```markdown
### Ubiquitous Requirements (Baseline)
- The system shall guarantee TypeScript type safety.
- The system shall provide cross-platform compatibility.

### Event-driven Requirements
- WHEN code is committed, the system shall run tests automatically.
- WHEN a build fails, the system shall notify developers immediately.

### State-driven Requirements
- WHILE in development mode, the system shall offer hot reloading.
- WHILE in production mode, the system shall produce optimized builds.

### Optional Features
- WHERE Docker is available, the system may support container-based deployment.
- WHERE CI/CD is configured, the system may execute automated deployments.

### Constraints
- IF a dependency vulnerability is detected, the system shall halt the build.
- Test coverage shall remain at or above 85%.
- Build time shall not exceed 5 minutes.
```

---

_This technology stack guides tool selection and quality gates when `/alfred:2-run` runs._
