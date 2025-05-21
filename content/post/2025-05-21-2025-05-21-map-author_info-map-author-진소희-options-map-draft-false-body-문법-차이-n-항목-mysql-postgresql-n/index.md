---
meta:
  title: MySQL vs PostgreSQL
  description: MySQL vs PostgreSQL, 프로젝트 선정기준
  date: 2025-05-21T23:19:00.000Z
author_info:
  author: 진소희
taxonomy:
  categories:
    - 데이터베이스
options:
  draft: false
date: 2025-05-21T23:23:53+09:00
---
### 문법 차이
| 항목               | MySQL                       | PostgreSQL                          |
|--------------------|-----------------------------|-------------------------------------|
| `AUTO_INCREMENT`   | `AUTO_INCREMENT` 사용        | `SERIAL`, `BIGSERIAL`로 대체         |
| `LIMIT` 문법        | `LIMIT 10 OFFSET 5`         | 동일                                 |
| `IF()` 함수         | 삼항 조건식으로 사용 가능     | `CASE WHEN` 문 사용                  |
| 문자열 연결         | `CONCAT(a, b)`              | 파이프 연산자 사용        |
| JSON 지원          | `JSON` 존재, 기능 제한적     | `JSONB`, 고급 연산자 + 인덱싱 지원  |


### 표현력과 기능 차이
| 항목        | MySQL              | PostgreSQL            |
| --------- | ------------------ | --------------------- |
| 윈도우 함수    | 제한적 (버전 8.0 이상 필요) | 풍부하게 지원               |
| 서브쿼리      | 일부 제한 있음           | 거의 모든 곳에서 사용 가능       |
| 사용자 정의 타입 | 불가                 | 가능 (예: ENUM, 복합 타입 등) |
| 저장 프로시저   | 존재하나 기능 제한         | 더 강력한 로직 처리 가능        |

### ORM (예: JPA, Sequelize 등) 사용 시
| 항목      | MySQL                | PostgreSQL                         |
| ------- | -------------------- | ---------------------------------- |
| 호환성     | 매우 높음                | 매우 높음                              |
| JSON 타입 | `TEXT`로 처리하거나 `JSON` | `JSONB`까지 지원하며 쿼리도 풍부              |
| 배열 타입   | 없음                   | `int[]`, `text[]` 등 배열 컬럼 직접 사용 가능 |

### PostgreSQL 선정이유

#### 1. 복잡한 관계형 구조에 최적화된 데이터 무결성 관리
- users, goals, tasks, subtasks 등 테이블 간 다단계 참조 관계가 많음
- PostgreSQL은 다음 기능으로 이를 안정적으로 관리함:
  - 외래키 제약조건의 강력한 enforcement
  - CHECK, DEFERRABLE, CASCADE 등 제약 조건 옵션 다양
  - Enum을 유형으로 직접 정의 가능 → 코드와 스키마 일치도 향상

#### 2. JSONB 지원 → 서브작업 생성 및 알림 기능 등 확장 용이
- tasks, notifications, calendar_syncs, external_events 등의 경우
- 메시지, 상태 변화 등 유연한 구조가 필요
- PostgreSQL은:
  - JSONB 타입 지원 (MySQL보다 훨씬 빠르고 인덱싱 가능)
  - 복잡한 서브구조 필터링 및 조건 기반 자동 작업 생성 등에 강함
  - WHERE data->>'status' = '진행중' 등의 직관적 JSON 쿼리 작성 가능

#### 3. 시간, 날짜, 기간 연산에 강력한 기능
- start_date, end_datetime, sleep_time, focus_start 등 시계열 필드 다수
- PostgreSQL은:
  - interval, timestamp with time zone, date_trunc, AGE() 등 풍부한 시간 함수
  - 예: 기간 종료 시 목표 상태 자동 변경 → WHERE end_date < NOW()
  - timezone-aware 연산으로 글로벌 사용자 대응에 유리

#### 4. 분석, 랭킹, 통계 기능에 강함
- user_analytics, reflections, daily_missions 등에서:
- 연속 달성 일수, 집중시간 분석, 실패 패턴 분석 등 집계/통계 연산 필요
- PostgreSQL은:
  - RANK(), ROW_NUMBER(), SUM() OVER () 등 윈도우 함수
  - 조건부 통계 → FILTER (WHERE is_completed)
  - materialized view 등으로 리포트 생성 최적화

#### 5. 미래 확장성 (추천 시스템, 리포트, AI 연동 등)에 유리
- 이후 추천 기능, 리포트 PDF화, 외부 캘린더 통합 등 확장이 예상됨
  
### DBML (Database Markup Language) 기준
우리 프로젝트는 기능이 복잡하고 데이터 관계가 풍부한 편

| 항목              | 추천 DB             | 이유                              |
| --------------- | ----------------- | ------------------------------- |
| 기본 호환성, 쉬운 세팅   | **MySQL**         | 접근성 높고 튜닝 없이도 무난하게 동작           |
| 데이터 무결성, 복잡한 쿼리 | **PostgreSQL**    | JSON, 복잡한 조인, 트랜잭션 강점           |
| 우리의 프로젝트 요구사항   | **PostgreSQL** 🏆 | 다양한 관계형 구조, 트랜잭션 안정성, 분석 기능에 강함 |
