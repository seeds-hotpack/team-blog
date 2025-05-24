---
meta:
  title: 스프링 핵심 개념원리
  date: 2025-05-24T23:01:00.000Z
  description: 스프링 핵심 개념원리
author_info:
  author: 진소희
options:
  draft: false
date: 2025-05-24T23:04:19+09:00
---
### 싱글톤 패턴
- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
```java
public class SingletonService {

    private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance() {
        return instance;
    }

    private SingletonService() {}

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```
1. static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 `getInstance`메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.

### 싱글톤 컨테이너(=스프링 컨테이너)
- 스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤으로 관리한다.

### 싱글톤 방식의 주의점
- 여러 클라이언트가 하나의 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
- 무상태(stateless)로 설계해야 한다.
  - 특정 클라이언트에 의존적인 필드가 있으면 안된다.
  - 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
  - 가급적 읽기만 가능해야 한다.
  - 픽드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

### @Configuration
- 스프링이 내부적으로 바이트코드를 조작해서 싱글톤이 보장되도록 처리
```java
@Bean
public MemberService memberService() {
    return new MemberServiceImpl(memberRepository());
}
@Bean
public OrderService orderService() {
    return new OrderServiceImpl(memberRepository(), discountPolicy());
}
```
- `memberRepository()`가 2번 호출되니 `new MemoryMemberRepository()`가 2번 실행되어 다른 인스턴스가 생성되는 것처럼 보인다.
- 이대로면 싱글톤이 깨지는 것 아닌가? 라는 의문이 생김
- 해결 방법: `@Configuration + CGLIB`
  - `@Configuration`이 붙으면, 스프링은 AppConfig 클래스를 바이트코드 조작하여 **AppConfig를 상속한 프록시 객체를 생성(CGLIB 기반)**하고
그 프록시 객체가 빈 메서드를 감싸서 동작을 바꿔준다.
- 실제로 일어나는 일 (CGLIB 프록시)
```java
@Override
public MemberRepository memberRepository() {
    // 이미 등록된 빈이 있으면 그걸 반환
    if (스프링컨테이너.containsBean("memberRepository")) {
        return getBean("memberRepository");
    }
    // 처음 호출 시만 실제 객체 생성
    else {
        MemberRepository repo = new MemoryMemberRepository();
        registerSingleton("memberRepository", repo);
        return repo;
    }
}
```
- @Configuration을 적용하지 않고 @Bean만 적용한다면?
  - AppConfig 클래스는 일반 POJO 객체로 등록됨
  - 각 @Bean 메서드는 그대로 호출되어 실행됨
  - memberService()와 orderService() 등에서 memberRepository()를 각각 호출하면, 각각 다른 객체가 생성됨

---

### ThreadLocal
> 자바에서 스레드마다 고유하게 값을 저장할 수 있게 해주는 클래스
즉, 같은 변수라도 각 스레드마다 독립된 복사본을 가짐

- 멀티스레드 환경에서 공유된 변수를 사용할 경우 동시성 문제가 생긴다.
- 그럴 때, 스레드마다 독립적인 값을 보관할 수 있는 ThreadLocal을 사용하면 문제 해결

### 컴포넌트 스캔과 의존관계 자동 주입
- 스프링은 설정정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다.
- 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공한다.
- 예시
```java
@Component
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired // 생성자가 1개면 생략 가능
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```
- memberRepository와 discountPolicy 빈이 자동으로 주입됨
### 탐색 패키지 시작 위치 지정
```java
@Configuration
@ComponentScan(basePackages = "hello.core.member")
public class AutoAppConfig {
}
```
- hello.core.member 패키지부터 하위만 스캔한다.
```java
@ComponentScan(basePackages = {"hello.core.member", "hello.core.order"})
```
- 여러 시작 위치를 동시에 설정 가능
```java
@ComponentScan(basePackageClasses = AutoAppConfig.class)
```
- AutoAppConfig.class가 속한 패키지를 기준으로 자동 설정
### 컴포넌트 스캔 기본 대상
| 애노테이션            | 설명                  | 공통점                |
| ---------------- | ------------------- | ------------------ |
| `@Component`     | 컴포넌트 스캔의 기본 대상      | 상위 어노테이션           |
| `@Controller`    | 스프링 MVC 컨트롤러로 인식    | 모두 `@Component` 포함 |
| `@Service`       | 서비스 계층 비즈니스 로직 담당   |                    |
| `@Repository`    | DAO 계층, 데이터 접근 담당   |                    |
| `@Configuration` | 설정 정보 클래스 (스프링 설정용) |                    |

### 필터
- FilterType 옵션
```java
public enum FilterType {
    ANNOTATION,     // 기본값, 애노테이션 기준
    ASSIGNABLE_TYPE,// 지정한 타입과 자식 타입을 인식해서 동작
    ASPECTJ,        // AspectJ 패턴 사용
    REGEX,          // 정규 표현식
    CUSTOM          // 사용자 정의
}
```
- 기본적으로 제공하는 스프링 부트의 기본 설정에 맞추어 사용하는것을 권장
### 중복 등록과 충돌
만약 수동 빈, 자동 빈 등록에서 빈 이름이 충돌된다면?
- 수동 빈 등록 vs 자동 빈 등록
- 수동 빈 등록이 우선권을 가진다 : 수동 빈이 자동 빈을 오버라이딩 해버린다.
```bash
The bean 'memoryMemberRepository', defined in class path resource [Hello/core/AutoAppConfig.class], could not be registered. A bean with that name has already been defined in file [/Users/jinsohee/Documents/dev/spring/core/out/production/classes/Hello/core/member/MemoryMemberRepository.class] and overriding is disabled.
```
- 최근 스프링 부트는 동일한 이름의 빈이 있으면 오류 발생 → 명시적으로 해결 요구한다.
- application.properties
```yml
spring.main.allow-bean-definition-overriding=true # 이렇게 설정해두면 오버라이딩된다.
```
이렇게 설정해두면 오버라이딩된다.
- 하지만 잡기 어려운 버그가 만들어질 수 있으니 안하는 것이 좋다.
