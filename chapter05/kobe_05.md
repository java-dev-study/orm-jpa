# 5. 연관 관계 매핑 기초

### 목표

- 객체와 테이블 연관관계의 차이를 이해
- 객체의 참조와 테이블의 외래 키를 매핑
- 용어 이해
- 방향 : 단방향, 양방향
- 다중성 : N:1, 1:N, 1:1, N:M
- 연관 관계 주인: 객체 양방향 연관관계는 관리 주인이 필요

**객체를 테이블에 맞추어 데이터 중심으로 모델링 하면 협력 관계를 만들 수 없다.**

- 테이블은 외래 키로 조인 해서 연관 테이블을 찾는다.
- 객체는 참조 Member.setTeamId(Team.getId())를 사용해서 연관된 객체를 찾는다.

### 단방향 연관관계 (N:1)

- 회원과 팀이 있다.
- 회원은 하나의 팀에만 소속될 수 있다.
- 회원과 팀은 다대일 관계이다.

```java
@ManyToOne
@JoinColum(name="TEAM_ID")

Team team = new Team();
team.setName("TeamA")
em.persist(team)

Member member = new Member();
member.setName("member1");
member.setTeam(team); // 단방향 연관관계 설정, 참조 저장
em.persist(member);

```

### 양방향 연관관계와 연관관계의 주인

```java
@oneToMany(mappedBy="team") //teamd으로 매핑이 되어 있는 것
private List<Member> members = new ArrayList<>();
```

- mappedBy : 객체와 테이블간에 연관관계를 맺는 차이를 이해해야 한다.
- 객체 연관관계 = 2개 [회원 → 팀 , 회원 ← 팀]
- 테이블 연관관계 = 1개 회원 ↔ 팀

테이블의 양방향 관계

- 외래키

객체의 양뱡향 관계

```java
class A {     class B {
B b;          A a;
}             }

A -> B(a.getB())
B -> A(b.getA())
```

둘 중 하나로 외래 키를 관리해야한다.

**연관관계의 주인**

양방향 매핑 규칙

- 객체의 두 관계중 하나를 연관관계의 주인으로 지정
- 연관관계의 주인만이 외래 키를 관리(등록, 수정)
- 주인이 아닌쪽은 읽기만 가능
- 주인은 mappedBy 속성 사용 X
- 주인이 아니면 mappedBy로 주인을 지정해야한다.

누구를 주인으로 해야하나.

- 외래 키가 있는 곳을 주인으로 정해라
- 여기서는 Member.team이 연관관계의 주인
- 다 쪽이 연관관계의 주인이 된다. Member와 Team에서 “Member” 
자동차와 바퀴 “바퀴

양방향 매핑 가장 많이 하는 실수

```java
Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setName("member1");

team.getMembers().add(member);
em.persist(member);       //DB 저장이 안된다.

Team team = new Team();
team.setName("TeamA");
em.persist(team);

Member member = new Member();
member.setUsername("member1");
member.setTeam(team);
em.persist(member);       //DB 저장이 된다.
```

**양쪽에다 값을 모두 세팅해줘야한다.**

- 순수 객체 상태를 고려해서 항상 양쪽에 값을 설정하자
- 연관관계 편의 메소드를 생성하자 - 값을 세팅하는 메소드는 편하게
- 양방향매핑시에 무한 루프를 조심하자
EX) toString(), lombok, JSON 생성 라이브러리

**양방향 매핑 정리**

- 단방향 매핑만으로도 이미 연관관계 매핑은 완료
설계 시 단방향 매핑으로 설계를 끝내야한다.
- 양방향 매핑은 반대 방향으로 조회 기능이 추가된 것 뿐
- JPQL에서 역방향으로 탐색할 일이 많음
- 단방향 매핑을 잘 하고 양방향은 필요할 때 추가해도 됨
(테이블에 영향을 주지 않음)

연관 관계의 주인을 정하는 기준

- 비즈니스 로직을 기준으로 연관관계의 주인을 선택하면 안됨
- **연관관계의 주인은 외래 키의 위치를 기준으로 정해야한다.**