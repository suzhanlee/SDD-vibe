---
id: PROJECT-001
version: 0.0.1
status: draft
created: 2025-10-23
updated: 2025-10-23
author: @suzhanlee
priority: critical
category: infrastructure
labels: [gradle, multi-module, cqrs, ddd]
scope: project-setup
---

# SPEC-PROJECT-001: Gradle 멀티 모듈 프로젝트 초기 세팅 (CQRS 기반 도메인 분리)

## HISTORY

### v0.0.1 (2025-10-23)
- **INITIAL**: Gradle 멀티 모듈 프로젝트 초기 세팅 SPEC 작성
- **AUTHOR**: @suzhanlee
- **SCOPE**: CQRS 패턴 기반 도메인 중심 멀티 모듈 구조 설계
- **CONTEXT**: E-commerce 학습 프로젝트의 인프라 기반 구축
- **MODULES**: common(공통 모듈), product(상품 도메인) 초기 생성

---

## Environment (환경)

### 기술 스택
- **Language**: Java 17+ (LTS)
- **Build Tool**: Gradle 8.x (Kotlin DSL)
- **Framework**: Spring Boot 3.x
- **Architecture**: 멀티 모듈 구조 (루트 프로젝트 + 서브 모듈)

### 프로젝트 구조
```
SDD-vibe-2/
├── settings.gradle.kts          # 루트 설정: 서브 모듈 포함
├── build.gradle.kts              # 루트 빌드: 공통 의존성 및 플러그인
├── common/                       # 공통 모듈
│   ├── build.gradle.kts
│   └── src/main/java/com/sdd/vibe/common/
└── product/                      # 상품 도메인 모듈
    ├── build.gradle.kts
    └── src/main/java/com/sdd/vibe/product/
        ├── command/              # 쓰기 작업 (CQRS)
        └── query/                # 읽기 작업 (CQRS)
```

### 아키텍처 패턴
- **CQRS (Command Query Responsibility Segregation)**: Command(쓰기)/Query(읽기) 분리
- **DDD (Domain-Driven Design)**: 도메인 중심 모듈 분리
- **Multi-Module**: 각 도메인이 독립 Gradle 서브 모듈

---

## Assumptions (전제 조건)

### 아키텍처 결정 사항
1. **CQRS 패턴 적용**:
   - Command(쓰기)/Query(읽기) 명확히 분리
   - 각 도메인 모듈 내부에서 command/query 패키지로 분리

2. **DDD 기반 도메인 중심 모듈 분리**:
   - 각 도메인(product, order, payment)이 독립 Gradle 서브 모듈
   - 각 모듈 내부에 Entity, Repository, Service, Controller 포함

3. **점진적 확장 전략**:
   - 초기에는 common, product 모듈만 생성
   - 나머지 도메인(order, payment, user, etc.)은 SPEC 작성 후 점진적 추가

### 기술 제약 사항
- 모든 모듈은 동일한 Spring Boot 버전 공유
- 도메인 모듈 간 직접 의존성 금지 (common 모듈을 통해서만 통신)
- 패키지 명명 규칙: `com.sdd.vibe.{모듈명}.{command|query}`

---

## Requirements (요구사항)

### Ubiquitous Requirements (보편 요구사항)
- 시스템은 Gradle 멀티 모듈 프로젝트 구조를 따라야 한다.
- 모든 도메인 모듈은 common 모듈을 참조해야 한다.
- 각 도메인 모듈은 CQRS 패턴을 따라 command/query로 분리되어야 한다.
- 모든 모듈은 독립적으로 빌드 가능해야 한다.

### Event-driven Requirements (이벤트 기반 요구사항)
- **WHEN** 새로운 도메인이 추가되면, 독립된 Gradle 서브 모듈로 생성해야 한다.
- **WHEN** 모듈 간 의존성이 추가되면, build.gradle.kts에 명시적으로 선언해야 한다.
- **WHEN** `./gradlew build` 실행 시, 모든 모듈이 순차적으로 빌드되어야 한다.

### State-driven Requirements (상태 기반 요구사항)
- **WHILE** 개발 환경에서, 모든 모듈은 동일한 Spring Boot 버전을 공유해야 한다.
- **WHILE** 빌드 과정에서, 순환 참조가 감지되면 즉시 실패해야 한다.

### Constraints (제약 조건)
- **IF** 모듈 간 순환 참조가 발생하면, Gradle 빌드는 실패해야 한다.
- 도메인 모듈 간 직접 의존성은 금지하며, common 모듈을 통해서만 통신해야 한다.
- 각 모듈의 패키지 구조는 `com.sdd.vibe.{모듈명}.{command|query}` 형식을 따라야 한다.
- common 모듈은 어떤 도메인 모듈에도 의존해서는 안 된다.

---

## Specifications (상세 명세)

### 1. 루트 프로젝트 설정

#### settings.gradle.kts
```kotlin
rootProject.name = "SDD-vibe-2"

// 서브 모듈 포함
include("common")
include("product")
```

#### 루트 build.gradle.kts
```kotlin
plugins {
    java
    id("org.springframework.boot") version "3.2.0" apply false
    id("io.spring.dependency-management") version "1.1.4" apply false
}

allprojects {
    group = "com.sdd.vibe"
    version = "0.4.10"

    repositories {
        mavenCentral()
    }
}

subprojects {
    apply(plugin = "java")
    apply(plugin = "org.springframework.boot")
    apply(plugin = "io.spring.dependency-management")

    java {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }

    dependencies {
        implementation("org.springframework.boot:spring-boot-starter")
        testImplementation("org.springframework.boot:spring-boot-starter-test")
        testImplementation("org.junit.jupiter:junit-jupiter")
        testImplementation("org.mockito:mockito-core")
    }

    tasks.test {
        useJUnitPlatform()
    }
}
```

### 2. common 모듈 설정

#### common/build.gradle.kts
```kotlin
dependencies {
    // 공통 의존성만 포함
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-validation")

    // 추후 추가 예정: 공통 예외, 응답 포맷, 유틸리티
}
```

#### 디렉토리 구조
```
common/
├── build.gradle.kts
└── src/
    ├── main/
    │   └── java/com/sdd/vibe/common/
    │       ├── exception/        # 공통 예외 (추후)
    │       ├── response/         # 공통 응답 포맷 (추후)
    │       └── util/             # 공통 유틸리티 (추후)
    └── test/
        └── java/com/sdd/vibe/common/
```

### 3. product 모듈 설정 (CQRS 구조)

#### product/build.gradle.kts
```kotlin
dependencies {
    // common 모듈 의존성
    implementation(project(":common"))

    // 도메인 특화 의존성
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    runtimeOnly("com.h2database:h2")
}
```

#### 디렉토리 구조 (CQRS 패턴)
```
product/
├── build.gradle.kts
└── src/
    ├── main/
    │   └── java/com/sdd/vibe/product/
    │       ├── command/                  # 쓰기 작업 (CQRS)
    │       │   ├── controller/
    │       │   ├── service/
    │       │   ├── domain/               # Entity, Aggregate
    │       │   └── repository/
    │       └── query/                    # 읽기 작업 (CQRS)
    │           ├── controller/
    │           ├── service/
    │           ├── dto/                  # DTO, Projection
    │           └── repository/
    └── test/
        └── java/com/sdd/vibe/product/
            ├── command/
            └── query/
```

---

## Traceability (@TAG)

### TAG 정의
- **SPEC**: @SPEC:PROJECT-001
- **CODE**:
  - @CODE:PROJECT-001:CONFIG (설정 파일)
    - `settings.gradle.kts`
    - `build.gradle.kts` (루트)
    - `common/build.gradle.kts`
    - `product/build.gradle.kts`
  - @CODE:PROJECT-001:STRUCTURE (디렉토리 구조)
    - `common/src/main/java/com/sdd/vibe/common/`
    - `product/src/main/java/com/sdd/vibe/product/command/`
    - `product/src/main/java/com/sdd/vibe/product/query/`
- **TEST**: @TEST:PROJECT-001
  - 빌드 검증 스크립트 (추후)
- **DOC**: @DOC:PROJECT-001
  - `docs/architecture/multi-module.md` (추후)

### TAG 검증 명령어
```bash
# SPEC ID 중복 확인
rg "@SPEC:PROJECT-001" -n .moai/specs/

# CODE TAG 확인
rg "@CODE:PROJECT-001" -n settings.gradle.kts build.gradle.kts common/ product/

# 전체 TAG 체인 검증
rg "@(SPEC|CODE|TEST|DOC):PROJECT-001" -n
```

---

## References (참고 자료)

### 내부 문서
- `.moai/project/structure.md`: 프로젝트 아키텍처 정의
- `.moai/project/tech.md`: 기술 스택 상세
- `.moai/memory/development-guide.md`: TRUST 5 원칙

### 외부 자료
- [Gradle Multi-Module Projects](https://docs.gradle.org/current/userguide/multi_project_builds.html)
- [Spring Boot Multi-Module](https://spring.io/guides/gs/multi-module/)
- [CQRS Pattern](https://martinfowler.com/bliki/CQRS.html)

---

## Next Steps (다음 단계)

1. **SPEC 승인 후**: `/alfred:2-run SPEC-PROJECT-001` 실행
2. **구현 완료 후**: `/alfred:3-sync` 실행하여 Living Document 업데이트
3. **후속 SPEC**:
   - SPEC-PRODUCT-001: 상품 등록 기능
   - SPEC-ORDER-001: 주문 처리 기능
   - SPEC-PAYMENT-001: 결제 처리 기능
