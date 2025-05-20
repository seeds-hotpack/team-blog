---
meta:
  title: 빌드도구에 대해 알아보자.
  date: 2025-05-21T00:05:00.000Z
  description: Ant, Maven, Gradle의 철학과 Gradle의 장점
author_info:
  author: 박준하
options:
  draft: false
  image: build_tools.png
date: 2025-05-21T00:09:49+09:00
taxonomy:
  categories:
    - spring-study
  tags:
    - ant
    - maven
    - gradle
    - 빌드도구
---
## 빌드 도구, 왜 중요할까?

개발을 하다 보면 코드를 작성하는 것 외에도 할 일이 정말 많다는 것을, 아실 겁니다. 당장 자바 코드만 해도, `.java` 파일을 **`javac` 명령어로 컴파일하여 `.class` 파일로 만들고**, 이 `.class` 파일들을 묶어 **JAR(Java Archive) 파일**을 생성하고, 또 이 JAR 파일이 필요로 하는 **외부 라이브러리(JAR 파일들)**까지 일일이 찾아서 클래스패스에 맞게 포함시켜야 비로소 실행 가능한 `.jar` 파일이 완성됩니다.

옛날에는 `javac` 명령어를 직접 입력하고, 라이브러리 경로를 수동으로 지정하는 것이 흔한 일이었습니다. 프로젝트 규모가 커지면 필요한 라이브러리만 수십, 수백 개에 달하고, 버전 충돌이라도 발생하면 머리가 지끈거릴 정도로 복잡하고 귀찮게 되었죠. 

바로 이러한 불편함과 복잡함을 획기적으로 간소화하고 자동화해주는 것이 **빌드 도구**입니다. 빌드 도구는 반복적이고 수동적인 이 모든 작업들을 대신 처리해줍니다. 그리하여 개발자의 소중한 시간을 절약하고, 사람의 실수를 줄이며, 팀원 간에 언제나 동일한 개발 환경을 유지할 수 있도록 돕는 없어서는 안 될 존재라 할 수 있습니다.

- - -

## 빌드 도구의 진화: Ant, Maven, 그리고 Gradle

자바 생태계의 빌드 도구는 시대의 흐름과 함께 발전하며 각기 다른 철학을 내세웠습니다.

### 1. Apache Ant (2000년): 극도의 유연성

![Apache Ant](data:image/webp;base64,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 "Apache Ant")

* **철학**: Ant는 마치 C/C++의 Make처럼, **극도의 유연성**을 중시했습니다. 어떤 프로젝트 구조나 규약도 강제하지 않았습니다.
* **특징**: XML로 태스크를 직접 수동 정의해야 했습니다. 이는 개발자가 모든 빌드 로직을 직접 스크립트로 작성해야 한다는 의미였습니다.
* **한계**: 반복적이고 수동적인 설정이 많아 유지보수가 어려웠고, 의존성 관리 기능이 없다는 치명적인 단점이 있었습니다.

### 2. Apache Maven (2004년): '관례 기반 구성'

![Maven](https://www.jrebel.com/sites/default/files/styles/social_preview_image/public/image/2020-07/image-blog-what-is-maven.jpg?itok=x9YxUgiO "Maven")

* **철학**: Ant의 단점을 보완하며 등장한 Maven은  '**관례(convention) 기반 구성**' 이라는 새로운 철학을 제시했습니다. `src/main/java`, `src/test/java`와 같은 표준 프로젝트 구조와 **POM(Project Object Model) 파일**에 정의된 규칙을 따르도록 강제했죠.
* **특징**: 중앙 저장소(Maven Central)와 통합된 **의존성 관리**, 그리고 컴파일, 테스트, 패키지 단계와 같은 **일관된 빌드 라이프사이클**을 제공하며 개발의 표준화를 크게 개선했습니다.
* **한계**: XML 기반의 선언적 설정은 강력했지만, 그만큼 유연성이 떨어져 규칙을 벗어나는 커스텀 작업은 복잡해졌습니다.

### 3. Gradle (2012년): '유연성과 관례의 조화'

![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSzNLAhrUbyBUeDClrqvvdgKMtwGa1S7fk_sJ3mj3gBUFdWUn0ME6-zttjwJabHc55_WAsqATFDJbTlNfNFWgpFLuvvJjzRaSi0sy8PgoU)

* **철학**: Ant의 유연성과 Maven의 관례를 영리하게 조합한 **Gradle**은 **차세대 빌드 도구**로 주목받았습니다. "코드는 스크립트처럼 간결하게, 그러나 필요한 규약은 활용"한다는 철학 아래 개발되었죠.
* **특징**: **Groovy 또는 Kotlin 기반의 DSL(Domain Specific Language)**을 사용해 **코드형 선언(code-over-configuration)** 방식을 도입했습니다. 이를 통해 빌드 스크립트를 훨씬 간결하고 강력하게 작성할 수 있게 되었어요.
* **강점**: **증분 빌드, 병렬 처리, 빌드 캐시**와 같은 혁신적인 기능으로 빌드 성능을 대폭 개선했으며, 풍부한 플러그인 생태계로 확장성과 재사용성을 극대화했습니다.

- - -

## Gradle의 철학과 핵심 기능 톺아보기

Gradle은 단순히 빠르고 유연한 것을 넘어, 개발자의 생산성을 극대화하기 위한 심오한 철학을 담고 있습니다.

### Gradle의 설계 철학: 선언적이면서도 유연한 빌드 구성

Gradle 빌드는 **Groovy/Kotlin DSL** 언어로 작성됩니다. 이 스크립트는 마치 실제 프로그래밍 코드처럼 동작하며, 개발자는 **조건문, 함수, 클래스 확장** 등을 활용해 복잡한 빌드 로직을 구현할 수 있습니다.

### 1. 태스크 기반 모델 (Task-based model)

Gradle은 \*\*태스크(Task)\*\*를 작업의 최소 단위로 다룹니다. 컴파일, JAR 생성, 테스트 실행 등 모든 독립적인 작업이 태스크로 표현됩니다. 태스크들은 서로 의존 관계를 설정하여 원하는 실행 순서를 유연하게 구성할 수 있습니다. 예를 들어, `compileJava` 태스크가 완료되어야 `jar` 태스크가 실행되도록 설정하는 식이죠.

### 2. 증분 빌드 (Incremental Build)

Gradle의 핵심 기능 중 하나는 **증분 빌드**입니다. 이전 빌드의 입력/출력 변경 여부를 정교하게 추적해, 변경이 없는 태스크는 다시 실행하지 않고 **UP-TO-DATE** 상태로 건너뜁니다. 예를 들어, 소스 코드 일부만 수정했다면 전체 프로젝트를 다시 컴파일하는 대신 변경된 부분만 재컴파일하여 빌드 시간을 획기적으로 단축합니다.

### 3. 빌드 캐시 (Build Cache)

**빌드 캐시**는 로컬 또는 원격 캐시를 활용해 이전 빌드 결과를 재사용하는 기능입니다. 한 번 생성된 아티팩트는 다른 환경(예: CI 서버)에서도 동일하게 재사용될 수 있습니다. CI 서버에서 빌드된 결과를 원격 캐시에 저장하고, 개발 환경에서 `--build-cache` 옵션을 켜면 동일한 태스크는 캐시에서 빠르게 결과를 가져와 빌드 속도를 더욱 높일 수 있습니다.

### 4. 병렬 실행 (Parallel Execution)

Gradle은 태스크와 의존성 그래프를 기반으로 가능한 작업들을 **병렬로 수행**합니다. 이를 통해 다중 모듈 프로젝트에서도 빌드 속도가 크게 향상됩니다. `Worker API`를 통해 매우 미세한 수준까지 병렬성을 확보하여 효율적인 리소스 활용이 가능합니다.

### 5. 의존성 관리 (Dependency Management)

Maven과 유사하게 `그룹:이름:버전` (예: `com.google.guava:guava:32.1.1-jre`) 형식으로 라이브러리를 선언하여 종속성을 관리합니다. Gradle은 여기에 클래스패스 설정, 버전 충돌 해결, Ivy 및 Maven 저장소 지원 등을 제공하며, 기본적으로 Maven Central과 같은 퍼블릭 저장소와 쉽게 연동됩니다.

### 6. DSL (Domain Specific Language)

Gradle 스크립트는 Groovy 또는 Kotlin 기반의 **DSL**로 작성됩니다. `plugins { id "java" }`와 같은 구문은 마치 일반 코드처럼 간결하고 직관적으로 프로젝트의 빌드 로직을 표현할 수 있게 해줍니다.

- - -

## Gradle 빌드를 구성하는 주요 요소들

Gradle 빌드는 여러 핵심 요소들로 구성됩니다. 각 요소는 프로젝트의 빌드 동작을 정의하는 중요한 역할을 합니다.

### 1. Plugins (플러그인)

Gradle 기능의 대부분은 **플러그인**에 의해 제공됩니다. Gradle 자체는 의존성 관리, 프로젝트 설정, 태스크 실행 등 '코어' 기능만 가지고 있으며, 컴파일, 테스트, 배포와 같은 구체적인 작업 로직은 플러그인으로 구현됩니다. 예를 들어, `java` 플러그인을 적용하면 자바 컴파일 및 테스트에 필요한 표준 태스크와 설정이 자동으로 추가됩니다. 플러그인은 빌드 로직을 캡슐화하여 재사용성을 높이는 데 기여합니다.

### 2. Repositories (저장소)

**저장소**는 프로젝트가 사용하는 라이브러리(의존성 아티팩트)를 가져올 위치를 지정합니다. `repositories` 블록에서 Maven Central, jcenter, 또는 사내 Nexus/Artifactory와 같은 저장소 URL을 지정할 수 있습니다.

```groovy
repositories {
    mavenCentral() // Maven Central에서 라이브러리 검색
    maven { url 'https://repo.example.com/maven2' } // 사내 저장소 추가
}
```

### 3. Dependencies (의존성)

`dependencies` 블록에서 프로젝트가 사용하는 라이브러리나 모듈을 선언합니다.

```groovy
dependencies {
    // 컴파일 및 런타임에 필요한 라이브러리
    implementation 'org.apache.commons:commons-lang3:3.12.0'
    // 테스트 시에만 필요한 라이브러리
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.1'
}
```

선언된 의존성은 내부적으로 \*\*설정(configurations)\*\*과 연결되어 적절한 클래스패스에 포함됩니다. Gradle은 그룹:이름:버전 형식 외에도 프로젝트 의존성(`project(':lib')`)이나 파일 의존성(`files('lib/a.jar')`)도 지원합니다.

### 4. Configurations (설정)

**설정(Configurations)**은 의존성의 범위를 정의하는 기능입니다. `implementation`, `testImplementation`, `runtimeOnly` 등 다양한 설정이 미리 정의되어 있어, 각각 "컴파일 시 필요한 라이브러리", "테스트 시에만 필요한 라이브러리" 등을 구분해 줍니다. 이를 통해 빌드 과정의 다양한 단계에서 의존성이 어떻게 사용될지를 정교하게 제어할 수 있습니다.

### 5. Tasks (태스크)

**태스크**는 Gradle이 수행하는 모든 작업 단위입니다. 컴파일, JAR 생성, 테스트 실행, 배포 등 모든 동작이 태스크로 표현됩니다. `compileJava`, `test`와 같은 태스크는 Java 프로젝트 플러그인에 의해 자동으로 등록됩니다. 개발자는 `tasks.register("myTask") { doLast { ... } }`와 같은 방법으로 커스텀 태스크를 추가하고, 태스크 간의 의존 관계를 설정하여 복잡한 빌드 파이프라인을 구축할 수 있습니다.

- - -

## 실제 프로젝트에서 어떻게 사용할까

### 1. 빌드 조건 분기

Gradle 스크립트는 코드로 작성되므로 **조건문, 변수, 프로젝트 속성** 등을 사용하여 빌드 로직을 유연하게 분기할 수 있습니다. 예를 들어, `gradle build -Penv=prod`와 같이 `-Penv=prod` 속성을 넘기면 배포 환경 설정을 다르게 적용하거나 특정 플러그인/태스크 적용 여부를 조건문으로 제어할 수 있습니다.

### 2. 커스텀 태스크 (Custom Task)

`DefaultTask` 클래스를 상속하거나 `tasks.register`를 사용하여 프로젝트에 필요한 독자적인 태스크를 정의할 수 있습니다. 외부 툴 호출, 특수 파일 작업 등 프로젝트에 특화된 로직을 태스크로 구현하여 `gradle myTask`처럼 직접 실행하거나 다른 태스크의 종속성으로 연결하여 활용할 수 있습니다.

### 3. 테스트 자동화

`java` 플러그인을 적용하면 `test` 태스크가 기본으로 생성되어 JUnit, TestNG 등의 테스트를 자동으로 실행합니다. JUnit5를 사용하려면 `tasks.test { useJUnitPlatform() }`와 같이 간단히 설정할 수 있습니다. Gradle은 테스트 결과를 XML/JUnit 보고서 형태로 출력하므로 CI 서버와의 연동도 매우 쉽습니다.

### 4. 다중 프로젝트 (Multi-project) 빌드

Gradle은 루트 프로젝트와 여러 서브프로젝트로 구성되는 **다중 프로젝트 빌드**를 지원합니다. `settings.gradle`에 `include 'app', 'lib'`처럼 모듈을 정의하고 서브프로젝트들 간의 의존 관계를 설정할 수 있습니다. 이 구조를 통해 대규모 프로젝트를 효율적으로 모듈화하고, 빌드 속도와 코드 재사용성을 모두 향상시킬 수 있습니다.

### 5. 빌드 스캔 (Build Scan)

`--scan` 옵션과 함께 빌드를 실행하면 웹 기반의 상세 빌드 리포트인 **빌드 스캔**을 생성할 수 있습니다. 빌드 스캔은 빌드 수행 과정을 시각화하고, 실행 시간, 의존성 그래프, 캐시 히트율 등 유용한 메트릭을 제공합니다. 이를 통해 빌드 성능 병목을 쉽게 분석하고 팀원과 문제 상황을 공유하기 편리합니다.

- - -

## Gradle `build.gradle` 파일의 기본 구조

아래는 전형적인 자바 애플리케이션 프로젝트의 `build.gradle` 예시입니다. 각 섹션의 의미를 이해하면 Gradle 파일을 훨씬 쉽게 다룰 수 있습니다.

```groovy
plugins {
    id 'java' // 자바 컴파일 및 테스트 태스크 추가
    id 'application' // 실행 가능한 JAR 생성 및 'run' 태스크 제공
}

group = 'com.example' // 프로젝트 그룹 ID
version = '1.0.0'     // 프로젝트 버전

repositories {
    mavenCentral()    // 의존성 저장소 지정: Maven Central에서 라이브러리 검색
}

dependencies {
    // 컴파일 및 런타임 의존성
    implementation 'org.apache.commons:commons-lang3:3.12.0'
    // 테스트 전용 의존성
    testImplementation 'org.junit.jupiter:junit-jupiter:5.9.1'
}

application {
    mainClass = 'com.example.Main' // 'application' 플러그인이 제공하는 속성
}

// 태스크 커스터마이징 예제
tasks.test {
    useJUnitPlatform() // JUnit5 테스트 활성화
}

// 커스텀 태스크 정의 예제
tasks.register('hello') {
    doLast {
        println 'Hello, Gradle!'
    }
}
```

* **`plugins` 블록**: 프로젝트에 필요한 빌드 기능을 활성화합니다. 여기서는 `java` 플러그인과 `application` 플러그인을 적용했습니다.
* **`group`, `version`**: 프로젝트의 메타데이터로, Maven과 호환되어 아티팩트 식별 및 배포에 사용됩니다.
* **`repositories` 블록**: 의존성(라이브러리)을 검색할 저장소를 지정합니다. `mavenCentral()`은 가장 기본적인 공개 저장소입니다.
* **`dependencies` 블록**: 프로젝트가 사용하는 외부 라이브러리 및 모듈을 선언합니다. `implementation`과 `testImplementation`은 의존성의 스코프를 구분하는 대표적인 설정입니다.
* **`application` 블록**: `application` 플러그인이 제공하는 설정으로, 실행 가능한 JAR를 생성할 때 메인 클래스 경로를 지정합니다.
* **`tasks` 블록**: 기본 태스크를 커스터마이징하거나(`tasks.test`), 새로운 커스텀 태스크(`tasks.register('hello')`)를 정의할 수 있습니다.

이 구조를 이해하면 `build.gradle` 파일의 각 섹션이 어떤 역할을 하는지 파악하고, 필요에 따라 코드를 추가하거나 수정하여 빌드 로직을 자유롭게 구성할 수 있습니다.

- - -

## Gradle vs. Maven 어떤 도구를 선택할까?

실무에서 빌드 도구를 선택할 때 Gradle과 Maven은 항상 고민의 대상입니다. 두 도구의 주요 차이점을 비교해 봅시다.

| 특징        | Gradle                         | Maven                          |
| --------- | ------------------------------ | ------------------------------ |
| **성능**    | 증분 빌드, 캐싱, 병렬 처리로 빌드 속도가 매우 빠름 | 전체 라이프사이클 수행으로 빌드 시간이 길어질 수 있음 |
| **선언 방식** | Groovy/Kotlin DSL 기반 (코드형 선언)  | XML 기반 (선언적)                   |
| **유연성**   | 코드 기반으로 커스텀 로직 작성이 매우 자유로움     | 엄격한 규약으로 커스텀 작업이 복잡할 수 있음      |
| **확장성**   | 플러그인 개발 및 적용이 용이               | Maven 플러그인 개발이 필요할 수 있음        |
| **가독성**   | 간결하고 명료하며, 스크립트처럼 읽기 쉬움        | 프로젝트 복잡화 시 XML이 길어져 가독성 저하 가능  |
| **학습 곡선** | DSL 학습 필요로 진입 장벽 약간 있음         | XML 컨벤션 익숙 시 비교적 시작하기 쉬움       |
| **강점**    | 대규모/복잡한 프로젝트, 성능 최적화, CI/CD    | 간단하고 표준화된 프로젝트, 기존 인프라 호환성     |

**요약하자면,**

* **Gradle**은 **대규모 프로젝트**나 **복잡한 빌드 로직**에 뛰어난 성능과 유연성을 제공합니다. 빌드 시간 단축, CI/CD 파이프라인 최적화에 강점이 있습니다.
* **Maven**은 **간단하고 표준화된 프로젝트**에 적합하며, XML 기반의 명확한 구조로 시작하기에 용이합니다. 기존 Maven 생태계와의 호환성이 중요할 때 유리할 수 있습니다.

최근에는 Gradle이 Maven의 장점을 대부분 흡수하며 점차 보편적인 빌드 도구로 자리매김하고 있습니다. 팀의 기술 역량과 프로젝트 요구사항을 고려하여 가장 적합한 도구를 선택하는 것이 중요합니다.

![]()

![]()
