# 4. 엔티티 매핑

### JPA 중요하게 봐야하는 것

- 영속성 컨텍스트 (JPA 내부 메커니즘)
- **실제 설계적인 측면 (객체, RDB 매핑)**

### 객체와 테이블 매핑

**@Entity**

- @Entity 가 붙은 클래스는 JPA가 관리, 엔티티라고 한다.
- JPA를 사용해서 테이블과 매핑할 클래스는 **@Entity** 가 필수

**주의**

- 기본 생성자 필수 (파라미터가 없는 Public 또는 Protected 생성자)
- final 클래스, enum, interface, inner 클래스 사용X
- 저장할 필드에 fianl 사용 x (DB에 저장할 필드에는 final 사용 불가.)

**@Entity 속성 : name**  

- JPA에서 사용할 엔티티 이름을 지정한다.
- default : 해당 클래스 이름 , 같은 클래스 없으면 기본 값 사용하기.

**@Table**

- name=”hello” : 매핑할 테이블 이름 default : 엔티티 이름을 사용
- catalog, schema, uniqueConstraints DDL 생성 시에 유니크 제약조건을 만든다. (DDL 기능)

### 데이터 베이스 스키마 자동 생성

- JPA는 DDL 시점에 테이블을 자동으로 생성 시켜준다. 
장점 : 객체를 만들면 알아서 테이블이 생기니까 더욱 객체 중심의 개발 가능
- 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성 
장점 : 어떤 DB를 사용하는지 바로 적용됌 (데이터베이스 방언)
- 이렇게 생성된 DDL은 개발 장비에서만 사용하기 → 운영에서 생성하기에는 위험하니 다듬고 사용하기.
”운영 장비에는 절대 create, create-drop, update 사용하면 안된다.”
- 개발 초기 단계에는 create or update
- 테스트 서버에는 update or validate
- 스테이징과 운영 서버는 validate 또는 none
- DDL 생성 기능은 DDL 자동 생성할 때만 영향주고 실행 로직에는 영향 X (Colunm, length, unique ..)

### 필드와 칼럼 매핑

- 요구 사항
- 회원은 일반 회원과 관리자 , 가입일 수정일, 설명 필드 필요(길이 제한 x)

```jsx
@Enumerated
private RoleType roleType // DB에는 Enum을 형식이 없기 때문에 RoleType 필요
// Enum은 ORDINAL 과 STRING 형식이 있다. ORDINAL은 순서로 STRING은 문자열로 -> Enum은 스트링으로 사용하자

@Temporal                 //날짜 타입 매핑
private Date createdDate

@Lob // 엄청 길고, 큰 길이도 가능하게 해준다.
```

**기본키** 

- @Id, @GeneratedValue
- 직접 할당 : @Id 만 사용
- 자동 생성 : Auto → DB 방언에 맞게 알아서 
                    IDENTITY → 기본키 생성을 DB에 위임 (Auto_Increment)
                    SEQUENCE → DB 시퀀스를 사용한다.
                    TABLE → 키 생성 전용 테이블을 따로 만들어서 생성한다. 모든 DB 적용 가능, 단점: 성능

**IDENTITY 전략**

- 기본 키 제약 조건 null 아님, 유일, **변하면 안된다.**
- 미래까지 변하지 않는 조건을 만족하는 자연키는 찾기 어렵다. 대체키를 사용하자. 
(자연키는 비즈니스 시 사용되는 주민등록번호, 학번 같은. 대체키 → 그냥 번호로)
- 권장 : Long형 + 대체키 + 키 생성 전략 , AUTO_INCREMENT, sequence_object

**특징**

- 기본 키 생성을 데이터베이스에 위임 (주로 MySQL,PostgreSQL ..에 사용) Auto_Increment
- GenerationType.IDENTITY

영속성 컨텍스트에 관리를 받기 위해서는 @Id가 필요하다. 하지만 IDENTITY를 모르기 때문에 커밋 시점이 아닌 
persist 시점에 insert를 처리해준다.

**SEQUENCE 전략**

-DB 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트. 
- persist() 실행 시 call next value for Member_seq 를 통해서 다음 Id 값을 가져와서 영속성 컨텍스트에 넣어준다. (아직은 DB에 안넣고 영속성에만 있다.) → 버퍼링 기능이 가능하다.

? DB를 계속 왔다갔다 하는데 성능 상 문제가 있지 않을까? → 
최적화 위해 시퀀스의 allocationSize 를 이용, 한 번에 가져오는 개수를 정할 수 있다. (allocationSize =50)

**Table 전략**

키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략

장점: 모든 데이터베이스에 적용 가능
단점: 성능

**실습 예제**

- 실습 예제 코드의 방식은 객체 설계 를 테이블 설계에 맞춘 방식

```java
Order order  = em.find(Order.class, 1L);
Long memberId = order.getMemberId();
// Order.getMember() -> 이런식으로 불러오는 것이 더욱 객체지향적이다.
```

- 테이블의 외래키를 객체에 그대로 가지고 온다
- 객체 그래프 탐색이 불가능하고 참조가 없어 UML도 잘못된다.