# 엔티티 매핑 

## 매핑 Annotation 
- 객체와 테이블 매핑 : @Entity, @Table 
- 기본 키 매칭 : @Id
- 필드와 컬럼 매핑 : @Column 
- 연관관계 매핑 : @ManyToOne, @JoinColumn

## @Entity 적용 시 주의 사항 
- 기본 생성자는 필수(파라미터가 없는 public 또는 protected 생성자)
- final 클래스 , enum, interface, inner 클래스에는 사용할 수 없다. 
- 저장할 필드에 final을 사용하면 안 된다. 
- 기본 생성자가 반드시 필요하다. 생성자를 하나 이상 만들었다면 기본 생성자도 같이 만들어 줘야 한다. 

## @Table
- uniqueConstraints : DDL생성 시에 유니크 제약조건을 만든다. 2개 이상의 복합 유니크 제약조건도 만들 수 있다. 

## DDL 생성 기능 
### 제약 조건 추가 
@Column (name = "NAME", nullable = false, length = 10) 
@Table (name = "MEMBER", uniqueConstraint = {@UniqueConstraint(name = "NAME_AGE_UNIQUE", columnNames = {"NAME","AGE"})})

## 기본 키 매핑 
- 직접 할당 : 기본 키를 애플리케이션에서 직접 할당 한다. 
- 자동 생성 : 대리 키 사용 방식 
 - IDENTITY : 키본 키 생성을 테이터베이스에 위임한다. 
 - SEQUENCE : 데이터베이스 시퀀스를 사용해서 기본 키를 할당한다.
 - TABLE : 키 생성 테이블을 사용한다. 

오라클 - 시퀀스 
MySQL - AUTO_INCREAMENT

### 기본키 직접 할당 
@Id 적용 가능한 자바 타입
- 자바 기본형 
- 자바 래퍼형
- String 
- java.util.Date
- java.sql.Date
- java.math.BigDecimal
- java.math.BigInteger

### IDENTITY 전략 
기본 키 생성을 데이터베이스에 위임 
데이터 베이스에 ID 컬럼을 비워두면 데이터베이스가 순서대로 값을 채워준다. 
```java 
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```
-> Statement.getGeneratedKeys()를 사용하면 데이터를 저장하면서 동시에 생성된 기본 키 값도 얻어 올 수 있다. 

### SEQUENCE 전략 
오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용할 수 있다. 

```java
@Entity 
@SequenceGenerator(
	name = "Board_SEQ_GENERATOR",
	sequenceName = "BOARD_SEQ",
	initialValue = 1, allocationSize =1)

```

### Table 전략 
```java
@Entity 
@SequenceGenerator(
	name = "Board_SEQ_GENERATOR",
	table = "MY_SEQUENCE",
	pkColumnValue = "BOARD_SEQ", allocationSize =1)

@Id 
@GeneratedValue(strategy = GenerationType.Table,
	generator = "Board_SEQ_GENERATOR")
```

## 필드와 컬럼 매핑 : 레퍼런스 
- @Column : 컬럼을 매핑한다. 
- @Enumerated : 자바의 enum 타입을 매핑한다. 
- @Temporal : 날짜 타입을 매핑한다. 
- @Lob : BLOB, CLOB 타입을 매핑한다. 
- @Transient : 특정 필드를 데이터베이스에 매핑하지 않는다. 
- @Access : JPA가 엔티티에 접근하는 방식을 지정한다. 







