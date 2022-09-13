# JPA 
## SQL을 직접 다룰 때 발생하는 문제점 
### CRUD 의 반복 구현 
### SQL 의 의존적인 개발 
 - 진정한 의미의 계층 분할이 어렵다 
 - 엔티티를 신뢰할 수 없다
 - SQL에 의존적인 개발을 피하기 어렵다
### JPA의 문제 해결 
저장기능 
``` java 
jpa.persist(member)
``` 
조회 기능 
``` java 
String memeberId ="helloId";
Member member = jpa.find(Member.class,memeberId);
```
수정기능
``` java 
Member member = jpa.find(Member.class,memberId);
member.setName("이름변경");
```
연관된 객체 조회
``` java 
Member member = jpa.find(Member.class,memberId);
Team team = member.getTeam();
```
## 패러다임의 불일치 
객체와 관계형 데이터베이스는 지향하는 목적이 서로 다르다. 객체 구조를 테이블 구조에 저장하는 데 한계가 있다. 
- JPA 상속 
``` JAVA 
jpa.persist(album);
insert into item...
insert into album...

String albumId = "id100";
Album album = jpa.find(Album.class, albumId);
select i.*,a.*
 from Item i  
 JOIN album a on i.item_id = a.item_id 
```
- 연관관계 
객체는 참조를 사용해서 다른 객체와 연관관계를 가지고 참조에 접근해서 연고나된 객체를 조회한다. 
반면에 테이블은 외래 키를 사용해서 다른 테이블과 연관관계를 가지고 조인을 사용해서 연관된 테이블을 조회한다. 
``` java 
class Member {
	Team team;

	Team getTeam() {
		return team;
	}
}
class Team {
	...
}

member.getTeam(); 

```
객체는 참조가 있는 방향으로만 조회할 수 있다. 
member.getTeam() 은 가능하지만 반대 방향인 team.getMember()는 참조가 없으므로 불가능 하다. 
반면에 테이블은 외래 키 하나로 MEMBER JOIN TEAM도 가능하지만 TEAM JOIN MEMBER 도 가능하다 

- 객체 그래프 탐색 
```java 
member.getOrder().getOrderItem()... 
```
객체 그래프를 어디까지 탐색할 수 있을까.. 

### JPA와 객체 그래프 탐색 
JPA를 사용하면 객체 그래프를 마음껏 탐색할 수 있다. 
JPA는 연관된 객체를 사용하는 시점에 적절한 SELECT SQL을 실행 한다. 
실제 객체를 사용하는 시점까지 데이터베이스 조회를 미룬다고 하여 지연 로딩이라 한다. 
```JAVA
//처음 조회 시점에 SELECT MEMBER SQL 
Member member = jpa.find(Member.class,memberId);

Order order = member.getOrder();
order.getOrderDate();//Order를 사용하는 시점에 SELECT ORDER SQL 
```
### 정리 
객체 모델과 관계형 데이터베이스 모델은 지향하는 패러다임이 서로 다르다. 
이 차이를 극복 하기 위해 개발자가 너무 많은 시간과 코드를 소비한다. 
JPA는 패러다임의 불일치 문제를 해결해주고 정교한 객체 모델링을 유지하게 도와준다. 

## JPA 란 
JPA(java Persistence API)는 자바 진영의 ORM 기술 표준이다. 
애플리케이션과 JDBC 사이에서 동작한다. 
JPA는 자바 ORM 기술에 대한 API 표준 
JPA를 구현한 ORM 프레임 워크 
- 하이버네이트 
- EclipseLink
- DataNucleus 
JPA USE 
- 생산성 
- 유지보수 
- 패러다임 불일치 해결 
- 성능 
- 데이터 접근 추상화와 벤더 독립성 






