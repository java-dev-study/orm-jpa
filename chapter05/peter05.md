# 연관관계 매핑 기초 
 객체의 참조와 테이블의 외래키를 매핑하는 것 
- 방향 - 단방향, 양방향 방향은 객체 관계에서만 존대 한다. 
- 다중성 - N:1, 1:N, 1:1, N:N 
- 연관관계의 주인


## 단방향 연관관계 
회원과 팀 간의 다대일 연관관계 
- 회원과 팀이 있다. 
- 회원은 하나의 팀에만 소속될 수 있다. 
- 회원과 팀은 다대일 관계다. 

양방향 관계 -> 서로 다른 방향 관계 2개이다.
객체는 참조로 연관관계를 맺고 테이블은 외래 키로 연관관계를 맺는다. 
객체를 양방향으로 참조하려면 단방향 연관관계 2개를 만들어야 한다. 
A-> B(a.b)
B-> A(b.a)
```java 
//연관관계 매핑
@ManyToOne
@JoinColumn(name="TEAM_ID")
private Team team;
```
@ManyToOne : 다대일(N:1) 관계, 회원과 팀은 다대일 관계이다. 연관관계를 매핑할 때 다중성을 나타내는 어노테이션을 필수로 사용한다. 
@JoinColumn : 외래키를 매핑할 때 사용한다. name 은 매핑할 외래 키 이름을 지정한다. 

@JoinColumn 생략 시 기본 전략 
필드명 + _ + 참조하는 테이블의 컬럼명 
team_TEAM_ID 외래키를 사용한다. 

optional : false 로 설정하면 연고나된 엔티티가 항상 있어야 한다. 
fetch : 글로벌 패치 전략을 설정한다. FetchType.EAGER, FetchType.LAZY
cascade : 영속성 전이 기능을 사용한다. 
targetEntity :  연관된 엔티티의 타입 정보를 설정 한다. 이 기능은 거의 사용하지 않음 

## 연관관계 사용 
조회 
- 객체 그래프 탐색 (객체 연관관계를 사용한 조회)
member.getTeam() 을 사용해서 member와 연관된 team 엔티티를 조회할 수 있다. 

- 객체 지향 쿼리 사용 (JPQL)
```
String jpql = "select m from Member m join m.team t where t.name=:teamName";
```

## 양방향 연관관계
```java 
@Entity 
public class Team {
    @Id
    @Column(name ="TAEM_ID")
    private String Id;

    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<Member<();
}
```
팀 엔티티에 컬렉션인 List<Member> members를 추가했다.
mappedBy 속성은 양방향 매핑일 때, 방대쪽 매핑의 필드 이름을 값으로 주면 된다. 


## 연관관계 주인 
두 객체 연관관계 중 하나를 정해서 테이블의 외해키를 관리해야 하는데 이것을 연관관계의 주인이라고 한다. 
연관관계의 주인만이 데이터베이스 연관관계와 매핑되고 외래 키를 관리 할 수 있다. 반면에 주인이 아닌쪽은 읽기만 할 수 있다. 
주인은 mappedBy 속성을 사용하지 않는다. 
주인이 아니면 mappedBy 속성을 사용해서 속성의 값으로 연관관계의 주인을 지정해야 한다.
연관관계의 주인은 외래 키가 있는 곳으로 정한다. 
```
데이터베이스 테이블의 다대일,일대다 관계에서는 항상 다 쪽이 외래 키를 가진다. 다 쪽인 @ManyToOne은 항상 연관관계의 주인이 되므로 MappedBy를 설정할 수 없다. 따라서 @ManyToOne에는 mappedBy 속성이 없다. 
```

## 양방향 연관관계 저장 
연관관계 주인이 아닌 쪽에서 입력된 값은 외래 키에 영향을 주지 않고 코드는 데이터 베이스에 저장할 때 무시된다. 
엔티티 매니저는 연관관계의 주인에 입력된 값을 사용해서 외래키를 관리한다. 

## 양방향 연관관계의 주의점 
- 연관관계의 주인에는 값을 입력하지 않고, 주인이 아닌 곳에만 값을 입력하는 것 
 -> 데이터베이스에 외래 키 값이 정상적으로 저장되지 않으면 이것부터 의심해본다. 
연관관계의 주인 만이 외래 키 값을 관리할 수 있다. 
객체의 관점에서 양쪽 방향에 모두 값을 입력해 주는 것이 가장 안전하다. 
양방향 연관관계는 결국 양쪽 다 신경 써야한다. 

### 연관관계 편의 메소드 
```java
public class Member {
    private Team team;

    public void setTeam(Team team){
        this.team = team;
        team.getMembers().add(this);
    }
    ...
}
```
setTeam() 메소드 하나로 양방향 관계를 모두 설정하도록 변경한다. 


teamB로 변경할 때 teamA -> member1 관계를 제거해야 한다. 
```java 
public void setTeam(Team team){

    if(this.team != null){
        this.team.getMembers().remove(this);
    }
    this.team = team;
    team.getMembers().add(this);
}
```
객체에서 서로 다른 단방향 연관관계 2개를 양방향인 것처럼 보이게한다. 
객체에서 양방향 연관관계를 사용하려면 로직을 견고하게 작성해야 한다. 

비즈니스 중요도를 배제하고 단순히 외래키 관리자 정도의 의미만 부여한다. 
연관관계의 주인은 외래 키의 위치와 관련해서 정해야지 비즈니스 중요도로 접근하면 안된다. 


