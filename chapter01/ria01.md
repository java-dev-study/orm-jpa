## Chapter01 JPA 소개

### 1.1 SQL을 직접 다룰 때 발생하는 문제점

- 자바 애플리케이션은 가장 대중적이고 신뢰할 만한 안전한 데이터 저장소로 관계형 데이터베이스를 사용함
- 데이터베이스는 객체 구조와 다른 데이터 중심의 구조를 가지기 때문에 객체를 데이터베이스에 직접 저장하거나 조회할 수 없음
- 객체 지향 애플리케이션과 데이터베이스 중간에서 SQL과 JDBC API를 사용해서 변환 작업 필요
- **객체를 데이터베이스에 CRUD하기 위해서는 SQL과 JDBC API 코드 작성에 많은 시간과 비용 소모**
- SQL에 의존하여 개발할 경우, DAO를 통해 어떤 SQL이 실행되고 어떤 객체들이 함께 조회되는지 확인 필요
- JPA를 사용하면 객체를 데이터베이스에 저장하고 관리할 때, SQL을 작성하지 않아도 됨

### 1.2 패러다임의 불일치

- 객체와 관계형 데이터베이스는 지향하는 목적이 다르기 때문에 패러다임 불일치 문제가 발생함
- 객체는 상속이라는 기능을 가지고 있지만, 테이블은 상속이라는 기능이 없음
  - JPA는 상속과 관련된 패러다임의 불일치 문제를 해결해줌
- **객체는 참조를 사용**해서 다른 객체와 연관관게를 가지고 **참조에 접근해서 연관된 객체를 조회**하지만, **테이블은 외래키를 사용**해서 다른 테이블과 연관관계를 가지고 **조인을 사용해서 연관된 테이블을 조회**함
  - 객체는 참조가 있는 방향으로만 조회할 수 있지만, 테이블은 외래 키 하나로 방향 상관없이 조인 가능

- **연관관계를 설정하고 객체를 저장하면 JPA는 참조를 외래키로 변환 후 쿼리를 데이터베이스에 전달함**
- 객체에서 참조를 사용해서 연관된 객체를 조회하는 것을 **객체 그래프 탐색**이라 하고, JPA를 사용하면 자유로운 객체 그래프 탐색이 가능함
- JPA를 사용하면 연관된 객체를 신뢰하고 마음껏 조회할 수 있는데 이는 실제 객체를 사용하는 시점까지 데이터베이스 조회를 미루는 지연 로딩 기능을 지원하기 때문임
- JPA는 같은 트랜잭션일 때 같은 객체가 조회되는 것을 보장함

### 1.3 JPA란 무엇인가?

- JPA(Java Persistence API)는 자바 ORM 기술에 대한 API 표준 명세임
- JPA를 사용해야 하는 이유
  - 생산성
  - 유지보수
  - 성능
  - 데이터 접근 추상화와 벤더 독립성
