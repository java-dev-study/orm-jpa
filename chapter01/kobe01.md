# JPA

## JPA 소개

**SQL 중심적인 개발의 문제점. -** 객체지향 언어와 SQL 쿼리 중심의 개발은 문제점을 야기한다.

- 무한 반복, 지루한 코드 (자바 객체를 SQL 로 SQL을 자바 객체로)
- 객체 vs 관계형 데이터베이스 패러다음의 불일치
- 객체를 SQL로 바꿔주는 역할을 해야한다. → 개발자들의 업무

객체와 관계형 데이터베이스의 차이

1. 상속
2. 연관관계
3. 데이터 타입
4. 데이터 식별 방법

조회할 때 번잡한 작업(SQL 조인과 객체로 만드는 작업을) → 자바 컬랙션 list.get() or list.save()로 해결

**연관관계**

객체는 참조를 사용 : member.getTeam() (객체는 단 방향)

테이블은 외래 키를 사용 : JOIN ON M.TEAM_ID = T.TEAM_ID (테이블은 양 방향)

테이블을 객체 지향 적으로 설계한다고 생각했을 때

```jsx
class Member {
String id;
Team team;              ----->  조회를 할 경우에 번거로운 작업이 필요해진다.
String username;
}
```

**엔티티 신뢰 문제**

```jsx
public void process()
{
		Member member = memberDAO.find(memberId);
		member.getTeam();                   /// 신뢰 불가
		member.getOrder().getDelivery();    /// 신뢰 불가
}
// 신뢰를 위해서 모든 객체를 미리 로딩할 수는 없으니, DAO를 사용해서 조회 기능 추가함
```

자바 컬랙션에서 조회

```jsx
Member member1 = list.get(memberID);
Member member2 = list.get(memberID);   /// ID로 검색하기 때문에 같다는 것을 알 수 있다.
```

**객체답게 모델링 할수록 매핑 작업만 늘어남 : 객체를 자바 컬랙션에 저장 하듯이 DB에 저장할수 없을까? → JPA**

### JPA

- Java Persistence API
- 자바 진영의 **ORM** 기술 표준

### ORM

- Object-relational Mapping (객체 관계 매핑)
- 객체는 객체대로 설계, 관계형 데이터베이스는 관계형 데이터 베이스대로 설계
- ORM 프레임워크가 중간에서 매핑

### JPA 동작

- 저장

MemberDAO(맴버 객체)→ JPA가 객체를 분석해서 쿼리문 Insert 생성, JDBC API 사용해서 DB 통신

- 조회

MemberDAO → JPA가 객체를 분석해서 적절한 select 쿼리 생성, JDBC API 사용해서 DB 통신

### JPA를 왜 사용해야하는가?

- SQL 중심적인 개발 →  객체 중심 개발
- 생산성

저장: jpa.persist(member) 조회: jpa.find(id) 수정: member.setName(””) 삭제 : jpa.remove(member)

- 유지보수

DB 칼럼이 추가될 때, 객체만 손 보면 된다. 알아서 처리해준다.

- 패러다임 불일치 해결
1. 상속 
jpa.persist, jap.find → 상속 관계를 JPA가 알아서 처리해준다. (조인 알아서 해서 저장, 조회 진행)
2. 신뢰할 수 있는 엔티티

```jsx
public void process()
{
		Member member = memberDAO.find(memberId);
		member.getTeam();                  
		member.getOrder().getDelivery();   
/// JPA를 사용했다면 믿을 수 있다. 신뢰할 수 있다.
}
```

 JPA 는 같은 트랜잭션에서 비교한 객체는 같은 객체임을 보장한다.

- 성능
1. 캐싱 기능

       같은 트랜잭션에서는 같은 엔티티를 반환하기 때문에 약간의 조회 성능을 향상

1. 트랜잭션을 지원하는 쓰기 지연- 한 번에 배치 프로그램 처럼 한번에 가능하게 한다.

        지연 로딩 : 객체가 실제 사용될 떄 로딩
        즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회

### 2장 JPA 시작

- ****엔티티 매니저 팩토리는 하나만 생성해서 애플리케이션 전체에
서 공유****
- ****엔티티 매니저는 쓰레드간에 공유X (사용하고 버려야 한다)****
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행
********
- JPA는 SQL을 추상화한 JPQL이라는 객체 지향 쿼리 언어 제공
- SQL 문법과 유사
- JPQL은 엔티티 객체를 대상으로 쿼리
- SQL은 데이터베이스 테이블을 대상으로 쿼리
- **테이블이 아닌 객체를 대상으로 검색하는 객체 지향 쿼리**
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존X
- JPQL을 한마디로 정의하면 객체 지향 SQL
****

```jsx
String jpql= "select m From Member m where m.name like '%hello%'";
List<Member> result = em.createQuery(jpql, Member.class).getResultList();
```