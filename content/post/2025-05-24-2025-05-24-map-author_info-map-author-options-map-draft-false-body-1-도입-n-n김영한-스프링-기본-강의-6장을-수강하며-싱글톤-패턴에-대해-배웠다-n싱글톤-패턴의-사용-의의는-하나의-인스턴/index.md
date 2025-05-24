---
meta:
  title: Singleton Pattern, ThreadLocal
  date: 2025-05-24T21:49:00.000Z
  description: 강의공유해주셔서감사합니다
author_info:
  author: ""
taxonomy:
  categories:
    - 스프링
options:
  draft: false
date: 2025-05-24T21:51:20+09:00
---
### 1. 도입

김영한 스프링 기본 강의 6장을 수강하며 싱글톤 패턴에 대해 배웠다.
싱글톤 패턴의 사용 의의는 하나의 인스턴스를 생성해 공유함으로써 **메모리 낭비를 줄이고**, **객체 생성을 반복하지 않아도 되는 효율성**을 확보하는 데 있다.

이때 하나의 인스턴스를 여러 스레드가 공유하기 때문에, **클라이언트에 의존적인 필드(예: 사용자별 요청 데이터)가 있으면 공유 값이 꼬여서 치명적인 버그**가 생길 수 있다.

그런데 선생님께서 읽고만 넘어가신 문장이 신경쓰였다.

> 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

**ThreadLocal이 뭐지?**
**공유 데이터는 절대 쓰면 안 되는 걸까? 공유하고 싶은 상황도 있지 않나?**
내 마 음 인 데 ?

---

### 2. 스프링이 싱글톤을 관리하는 방식

우리가 배운 스프링 컨테이너 (ApplicationContext)는 DI(의존성 주입) 컨테이너로서,
빈을 생성하고 보관하며 의존성을 주입하는 등 중앙 저장소 역할을 한다.

```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {
    private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

    public void registerSingleton(String beanName, Object singletonObject) throws IllegalStateException {
        synchronized (this.singletonObjects) {
            Object oldObject = this.singletonObjects.get(beanName);
            if (oldObject != null) {
                throw new IllegalStateException("Could not register object [" + singletonObject + "] under bean name '" + beanName + "': there is already object [" + oldObject + "] bound");
            } else {
                this.addSingleton(beanName, singletonObject);
            }
        }
    }
    // ... 생략
}
```

빈을 꺼낼 때는 `ConcurrentHashMap`이나 `synchronized` 같은 기법을 사용하여 **여러 요청이 동시에 들어와도 문제 없이 동작하도록(=스레드 세이프하게)** 구현돼 있다.

* **synchronized**: 여러 스레드가 하나의 공유 자원에 동시에 접근하지 못하도록 막음
* **ConcurrentHashMap**: HashMap은 동기화되지 않아 멀티스레드 환경에 안전하지 않음 → 대신 **세분화된 락**으로 병렬 접근이 가능

> 웹 서버는 요청마다 스레드가 새로 생성되므로, 동시에 같은 빈을 꺼내 쓰는 구조이다.
> 따라서 컨테이너가 스레드 세이프하지 않으면 데이터가 꼬인다!

#### ✅ 스프링이 책임지는 것

* 싱글톤 빈을 만드는 것
* 요청마다 안전하게 빈을 꺼내오는 것 (스레드 세이프하게)

#### ❌ 우리가 책임져야 할 것

* **빈 내부의 상태**가 있다면 동시성 문제가 발생할 수 있음 → 반드시 무상태(stateless)로 설계해야 함!

---

### 3. 상태가 필요한 경우: Thread Safe 자료구조 & ThreadLocal

그래도 상태를 갖고 싶을 수 있다. 이런 경우에는 어떻게 해야 할까?
대표적인 해결책은 다음과 같다:

| 항목         | ThreadLocal                  | ConcurrentHashMap      |
| ---------- | ---------------------------- | ---------------------- |
| **데이터 범위** | 스레드마다 고유                     | 여러 스레드에서 공유            |
| **주 용도**   | 요청마다 다른 정보, 로깅 컨텍스트, 트랜잭션 ID | 메모리 캐시, 조회수 관리 등 공유 자원 |
| **동작 원리**  | 각 스레드 스택에 별도로 보관             | 락/분할 락으로 안전하게 데이터 공유   |

---

### 4. ThreadLocal이란?

ThreadLocal은 JDK 1.2부터 제공되는 기능으로, **스레드마다 별도의 값을 저장**할 수 있는 메커니즘이다.

* 변수 자체는 전역처럼 보이지만, 내부적으로는 각 스레드별로 고유한 값을 가진다.
* 즉, **같은 ThreadLocal 변수라도 다른 스레드에서는 서로 다른 값을 저장**하고 있다.

> 비유하자면, 회사마다 공용 물품 보관함이 있지만, 직원 ID(=스레드 ID)별로 따로 관리되는 사물함처럼 생각하면 된다.

#### 주요 메소드 및 예시

1. `set(T value)`

```java
ThreadLocal<String> threadLocal = new ThreadLocal<>();
threadLocal.set("Hello, ThreadLocal!");
```

2. `get()`

```java
String value = threadLocal.get(); // "Hello, ThreadLocal!"
```

3. `remove()` → 반드시 호출해 메모리 누수 방지!

```java
threadLocal.remove();
```

4. `withInitial(Supplier<? extends T> supplier)` → 초기값 지정

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 42);
System.out.println(threadLocal.get()); // 42
```

#### 동작 구조

* 내부적으로 `ThreadLocalMap`이라는 자료구조를 사용
* `Thread.currentThread()`를 키로, 값은 우리가 저장한 오브젝트

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    Object value;
    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

---

### 5. 실전 예시

```java
public class ThreadLocalWithInitialExample {
    private static final ThreadLocal<String> threadLocalName = ThreadLocal.withInitial(() -> "기본이름");

    public static void main(String[] args) {
        Thread threadA = new Thread(() -> {
            System.out.println("[" + Thread.currentThread().getName() + "] 초기값: " + threadLocalName.get());
            threadLocalName.set("스레드A 이름");
            System.out.println("[" + Thread.currentThread().getName() + "] 설정된 이름: " + threadLocalName.get());
            threadLocalName.remove();
        }, "스레드A");

        Thread threadB = new Thread(() -> {
            System.out.println("[" + Thread.currentThread().getName() + "] 초기값: " + threadLocalName.get());
            threadLocalName.set("스레드B 이름");
            System.out.println("[" + Thread.currentThread().getName() + "] 설정된 이름: " + threadLocalName.get());
            threadLocalName.remove();
        }, "스레드B");

        threadA.start();
        threadB.start();
    }
}
```

📌 위 코드는 각각의 스레드가 **자신만의 값을 안전하게** 저장하고 꺼내는 과정을 보여준다.

---

### 6. 활용 예: Spring Security

Spring Security에서는 인증 정보를 저장하는 `SecurityContextHolder`가 내부적으로 `ThreadLocal`을 사용한다.

* 요청마다 다른 사용자가 접속하기 때문에 인증 정보가 섞이지 않도록 분리되어야 한다.
* `ThreadLocal`은 각 요청을 처리하는 스레드마다 독립적인 저장소를 제공하여, **사용자 정보가 다른 요청에 섞이지 않도록 보장**한다.

[출처: Spring Security 공식 문서](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-architecture-securitycontext)

---

### 7. 결론

* **싱글톤 빈은 스프링이 스레드 세이프하게 관리한다.**
* 하지만 빈 내부에 상태(필드)가 존재하면 동시성 문제가 발생할 수 있으므로, 설계 단계에서 stateless하게 만드는 것이 핵심이다.
* 상태가 꼭 필요하다면, **ThreadLocal**이나 **ConcurrentHashMap** 같은 도구를 활용하여 안전하게 관리해야 한다.

ThreadLocal은 요청별 사용자 정보를 저장하거나, 트랜잭션 컨텍스트 등
**스레드에 종속된 데이터를 안전하게 저장**할 때 매우 유용하다.

> "공유하면 위험하니까 하지 마"가 아니라,
> "공유할 땐 제대로 설계하고, 공유하지 말아야 할 정보는 ThreadLocal을 써라"가 핵심!


참고 출처
https://dev.gmarket.com/62
https://wildeveloperetrain.tistory.com/271
https://seunghyunson.tistory.com/28
https://coasis.tistory.com/60
https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html#servlet-authentication-architecture-securitycontext
