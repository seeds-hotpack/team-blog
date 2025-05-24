---
meta:
  title: JUnit, 왜 그리고 어떻게 써야돼?
  description: 테스트 코드 작성 전 JUnit과 AssertJ에 대해 복습해보았습니다.
  date: 2025-05-24T18:54:00.000Z
author_info:
  author: 박성열
taxonomy:
  categories: []
options:
  draft: false
date: 2025-05-24T18:58:31+09:00
---
![poster](https://velog.velcdn.com/images/yeo___li/post/e3342f70-799b-4eaa-b01c-433fdd0d2132/image.jpeg)

## 📚 목차
1. [JUnit 이란?](#1-junit-이란)
    - [1.1 단위테스트란?](#11-단위테스트란)
    - [1.2 JUnit을 사용하는 이유는?](#12-junit을-사용하는-이유는)

2. [JUnit 기초](#2-junit-기초)
    - [2.1 기본 Annotation](#21-기본-annotation)
        - [2.1.1 @Test](#211-test)
        - [2.1.2 @BeforeEach / @AfterEach](#212-beforeeach--aftereach)
        - [2.1.3 @BeforeAll / @AfterAll](#213-beforeall--afterall)
        - [2.1.4 @DisplayName](#214-displayname)
        - [2.1.5 @Disabled](#215-disabled)
    - [2.2 Assertions 종류](#22-assertions-종류)
        - [2.2.1 assertEquals](#221-assertequals)
        - [2.2.2 assertTrue](#222-asserttrue)
        - [2.2.3 assertThat + AssertJ](#223-assertthat--assertj)
        - [2.2.4 @Nested 테스트](#224-nested-테스트)
        - [2.2.5 @ParameterizedTest - 반복 테스트](#225-parameterizedtest---반복-테스트)

## 1. JUnit 이란?
JUnit은 Java에서 **단위 테스트** 코드를 작성할 수 있게 도와주는 프레임워크입니다.

### 1.1 단위테스트란?
소스코드의 특정 모듈(프로그램 내 하나의 기능을 부르는 말)이 의도된 대로 정확히 작동하는지 검증하는 절차이며,

함수, 메서드, 개별 코드 같은 작은 단위에 대해 테스트 케이스(Test Case)로 분리하고 테스트 코드를 작성하여 테스트하는 것을 의미합니다.

외부 API와의 연동이 필수라든가 DB 데이터 접근 등 외부 리소스를 직접 사용해야 하는 테스트라면 단위 테스트가 아닙니다. 단위 테스트에서 외부와의 연동이 필요하다면 테스트 대역(Test Double)을 사용하면 됩니다.

### 1.2 JUnit을 사용하는 이유는?

**1. Java 진영의 사실상 표준 테스트 프레임워크**
- Spring과 Gradle, Maven, IntelliJ, Jekins 등 주요 툴이 Junit을 지원하고 있기 때문입니다.

**2. 코드와 테스트를 같은 언어(Java)로 작성 가능**
- 테스트 전용 문법(DSL) 없이 순수 자바 문법으로 테스트가 가능하기 때문입니다.

**3. 테스트 자동화와 CI/CD 연동이 쉬움**
- GitHub Actions, Jenkins, Travies CI등에서 JUnit 테스트 결과를 자동으로 분석해주기 때문입니다.

***

## 2. JUnit 기초

### 2.1 기본 Annotation

#### 2.1.1 @Test
`@Test`는 테스트 메서드라고 선언해주는 어노테이션입니다.

```java
@Test
void testSum() {
    int result = 2 + 3;
    assertEquals(5, result);
}
```

#### 2.1.2 @BeforeEach/@AfterEach

- `@BeforeEach`는 각 테스트 메서드 실행 전, 실행되는 메서드를 선언해주는 어노테이션입니다.
- `@AfterEach`는 각 테스트 메서드 실행 후, 실행되는 메서드를 선언해주는 어노테이션입니다.

```java
@BeforeEach
void setup() {
    System.out.println("테스트 시작 전 준비!");
}

@AfterEach
void teardown() {
    System.out.println("테스트 끝난 후 정리!");
}
```

#### 2.1.3 @BeforeAll/@AfterAll

- `@BeforeAll`은 테스트 클래스를 초기화 할 때 딱 한 번 수행되는 메서드를 선언해주는 어노테이션입니다.
- `@AfterAll`은 테스트 클래스 내의 테스트 메서드를 모두 실행시킨 후 딱 한 번 수행되는 메서드를 선언해주는 어노테이션 입니다.
- `@BeforeAll`/`@AfterAll`을 사용할 메서드는 static으로 선언해야 합니다.


```java
@BeforeAll
static void beforeAll() {
	System.out.println("테스트 시작 전 최초 1회 수행!");
}
    
@AfterAll
static void afterAll() {
    System.out.println("테스트 종료 후 1회 수행!");
}
```

#### 2.1.4 @DisplayName

`@DisplayName("")`은 테스트 이름을 사람이 읽기 쉽게 지정해주는 어노테이션입니다.

```java
@DisplayName("더하기 기능 테스트")
@Test
void testAddition() {
    assertEquals(4, 2 + 2);
}
```

#### 2.1.5 @Disabled
`@Disabled`은 무시하고 싶은 테스트 메서드를 지정하는 어노테이션입니다.
```java
@Disabled
@Test
void testAddition() {
	System.out.println("이 메서드는 실행되지 않습니다!")
}
```


### 2.2 Assertions 종류

#### 2.2.1 assertEquals(expected, actual)
`assertEquals()`는 기대값과 실제값이 같은지 아닌지를 테스트 할 때 사용합니다.
```java
// then
assertEquals(10, 5 + 5);
```

#### 2.2.2 assertTrue(Condition)
`assertTrue()`는 조건식이 참인지 아닌지를 테스트 할 때 사용합니다.
```java
// then
assertTrue(5 > 3);
```

#### 2.2.3 assertThat() + AssertJ

`assertThat()`은 AssertJ를 사용하여 테스트에서 값을 좀 더 사람처럼 표현하면서 비교할 수 있게 도와주는 방식입니다.

> **AssertJ 란?**
>테스트 코드 안에서 값을 검증할 때 사용하는 "Assertion 라이브러리"
>[AssertJ 공식 문서 사이트](https://assertj.github.io/doc/)

JUnit에서도 지원하는 기본 Assertion 라이브러리가 있지만, 더 다양한 기능을 사용하기 위해 대부분 JUnit과 AssertJ를 함께 사용합니다.


> **JUnit vs AssertJ 차이점**
>
>|항목|JUnit|AssertJ|
>|--|------|-------|
>|종류|테스트 프레임워크|Assertion 라이브러리|
>|핵심 역할|테스트 실행, 구조 제공|테스트 내부 검증 표현|
>|테스트 어노테이션|@Test, @BeforeEach, ...|없음(단독 실행 불가)|
>|Assertion 기능|기본 제공|확장 제공|


이제 assertThat의 문법의 예시 대해 알아보겠습니다.

**1. 숫자 테스트 코드 예시**
```java
int result = 10;

assertThat(result)
    .isEqualTo(10)
    .isGreaterThan(5)
    .isLessThanOrEqualTo(10)
    .isPositive()
    .isNotZero();
```

**2. boolean 테스트 코드 예시**
```java
boolean flag = true;

assertThat(flag)
    .isTrue();
```

**3. 문자열 테스트 코드 예시**
```java
String name = "yeoli";

assertThat(name)
    .isNotEmpty()
    .startsWith("ye")
    .endsWith("li")
    .contains("eo")
    .hasSize(5);
```

**4. List 테스트 코드 예시**
```java
List<String> users = List.of("yeoli", "minwoo", "junsuh");

assertThat(users)
    .hasSize(3)
    .contains("yeoli", "junsuh")
    .doesNotContain("hacker");
```

**5. Map 테스트 코드 예시**
```java
Map<String, Integer> scores = Map.of("yeoli", 90, "minwoo", 80);

assertThat(scores)
    .containsKey("yeoli")
    .containsEntry("minwoo", 80)
    .doesNotContainKey("hacker");
```

**6. 객체 테스트 코드 예시**
```java
User user = new User("yeoli", 25);

assertThat(user)
    .isNotNull()
    .extracting("name", "age") // 특정 필드를 추출하여 검증 가능
    .containsExactly("yeoli", 25);
```

**7. Optional 테스트 코드 예시**
```java
Optional<String> result = Optional.of("yeoli");

assertThat(result)
    .isPresent()
    .contains("yeoli")
    .hasValueSatisfying(name -> assertThat(name).startsWith("ye"));
```

**8. 예외 테스트 코드 예시**
```java
assertThatThrownBy(() -> {
	// 검증하고 싶은 메서드 입력
    throw new IllegalArgumentException("잘못된 입력입니다!");
})
    .isInstanceOf(IllegalArgumentException.class)
    .hasMessageContaining("잘못된");
```

**9. 날짜 & 시간 테스트 코드 예시**
```java
LocalDate today = LocalDate.now();

assertThat(today)
    .isBeforeOrEqualTo(LocalDate.now())
    .isAfter(LocalDate.of(2020, 1, 1));
```

#### 2.2.4 @Nested 테스트
`@Nested`는 테스트 집합을 계층 구조로 그룹화 할 수 있는 어노테이션 입니다.

```java
class LoginTest {
	
    @Test
    @Nested
    @DisplayName("성공")
    class Scucess {
    	@Test
        @DisplayName("성공 시 200 반환")
        public void testExample() {...}
        
        @Test
        @DisplayName("~시 ~를 해야한다.")
        public void testExample2() {...}
    }
    
    @Test
    @Nested
    @DisplayName("실패")
    class Failure {
    	@Test
        @DisplayName("실패 시 400 반환")
        public void testExample() {...}
        
        @Test
        @DisplayName("~시 ~를 해야한다.")
        public void testExample2() {...}
    }
}
```

>**테스트 실행 결과**
>```
>✅성공
>	✅성공 시 200 반환
>	✅~시 ~를 해야한다.
>✅실패
>	✅실패 시 400 반환
>	✅~시 ~를 해야한다.
>```


#### 2.2.5 @ParameterizedTest - 반복 테스트

`@ParameterizedTest`는 한 테스트 메서드에 대해 다른 값으로 여러번 테스트 할 수 있도록 하는 어노테이션 입니다.

**1. 기본 테스트 구조**
`@ValueSource()`를 사용하여 테스트 값을 입력합니다.
```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

@ParameterizedTest
@ValueSource(ints = {1, 2, 3, 4}) // ValueSource를 이용하여 여러 값 입력
void isPositive(int number) {
    assertTrue(number > 0);
}
```

**2. 매개변수가 2개 이상일 때 테스트**
`@CsvSource()`를 사용하여 테스트 값을 입력합니다.
```java
@ParameterizedTest
@CsvSource({
    "yeoli, 25",
    "minwoo, 20",
    "junsuh, 30"
})
void testNameAndAge(String name, int age) {
    assertThat(name).isNotEmpty();
    assertThat(age).isGreaterThan(0);
}
```

**3. 컬렉션 및 커스텀 객체 테스트**
`@MethodSource`를 사용하여 테스트 값을 입력합니다.
`@MethodSource` 메서드는 static이고 Stream<...>을 리턴해야 합니다.

- 컬렉션 입력 방법
```java
static Stream<List<String>> provideNameLists() {
    return Stream.of(
        List.of("yeoli", "minwoo"),
        List.of("junsuh", "hacker")
    );
}

@ParameterizedTest
@MethodSource("provideNameLists")
void testNameList(List<String> names) {
    assertThat(names).isNotEmpty();
    assertThat(names).allSatisfy(name -> assertThat(name).isNotBlank());
}
```

- 커스텀 객체 입력 방법
```java
record User(String name, int age) {}

static Stream<User> provideUsers() {
    return Stream.of(
        new User("yeoli", 25),
        new User("minwoo", 22)
    );
}

@ParameterizedTest
@MethodSource("provideUsers")
void testUser(User user) {
    assertThat(user.name()).isNotEmpty();
    assertThat(user.age()).isGreaterThan(0);
}
```

**4. Enum 값 테스트**
`@EnumSource`를 이용하여 테스트 값을 입력합니다.
```java
enum Status { ACTIVE, INACTIVE, PENDING }

@ParameterizedTest
@EnumSource(Status.class)
void testEnum(Status status) {
    assertThat(status).isNotNull();
}
```

***
지금까지 JUnit과 AssertJ를 이용한 단위 테스트 방법에 대해 알아보았습니다.
감사합니다!

> **참조**
> [@Nested를 활용하여 계층 구조의 테스트 코드 작성하기](https://velog.io/@jhbae0420/Nested%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%98%EC%97%AC-%EA%B3%84%EC%B8%B5-%EA%B5%AC%EC%A1%B0%EC%9D%98-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0)
> [[JAVA] UnitTest에서 사용하는 AssertJ의 AssertThat이란?](https://hseungyeon.tistory.com/328)
> [JUnit의 개념과 장점, 단위 테스트의 개념과 장점, 단위/통합/기능 테스트](https://beststar-1.tistory.com/27)
