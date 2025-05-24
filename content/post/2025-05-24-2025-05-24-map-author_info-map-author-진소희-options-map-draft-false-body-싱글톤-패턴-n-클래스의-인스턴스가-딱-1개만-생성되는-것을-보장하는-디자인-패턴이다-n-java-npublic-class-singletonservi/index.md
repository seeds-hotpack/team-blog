---
meta:
  title: 스프링 핵심 개념원리
  date: 2025-05-24T23:01:00.000Z
  description: 스프링 핵심 개념원리
author_info:
  author: 진소희
options:
  draft: false
date: 2025-05-24T23:01:32+09:00
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
