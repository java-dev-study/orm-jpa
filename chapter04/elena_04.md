## 엔티티 매핑

- 엔티티와 테이블을 정확히 매핑하는 것이 가장 중요
- **객체와 테이블 매핑** : `@Entity`, `@Table`
- **기본 키 매핑** : `@Id`
- **필드와 컬럼 매핑** : `@Column`
- **연관관계 매핑** : `@ManyToOne`, `@JoinColumn`

---

**1. @Entity**
- JPA를 사용해서 테이블과 매핑할 클래스에 필수로 붙여야 하는 Annotation
- 주의사항
> - 기본 생성자는 필수
> - final, enum, interface, inner 클래스에서는 사용 불가
> - 저장할 필드에 final 사용 불가

---

**2. @Table**
- 엔티티와 매핑할 테이블 지정
- 생략 시 매핑한 엔티티 이름을 테이블 이름으로 사용

---

**3. 다양한 매핑 사용**

---

**4. 데이터베이스 스키마 자동 생성**
- JPA는 DB 스키마를 자동으로 생성하는 기능을 지원\
→ 매핑정보와 데이터베이스 방언을 사용해서 DB 스키마 생성\
`cf) 방언 : SQL 표준을 지키지 않거나 특정 DB만의 고유한 기능`
- `persistence.xml`에 아래 속성 추가\
→ 애플리케이션 실행 시점에 DB 테이블을 자동으로 생성
```xml
<property name="hibernate.hbm2ddl.auto" value="create" />
```
- 자동 생성되는 DDL은 지정한 DB 방언에 따라 달라짐
- 스키마 자동 생성 기능이 만든 DDL은 운영 환경에서 사용할 만큼 완벽하지는 않음

---

**5. DDL 생성 기능**
- `@Column` 매핑정보의 `nullable` 속성 값 `false`로 지정\
→ 자동 생성되는 DDL에 `not null` 제약조건 추가
- `@Column` 매핑정보의 `length` 속성 값 지정\
→ 자동 생성되는 DDL에 문자 크기 지정
- `@Table`의 `uniqueConstraints` 지정\
→ `유니크` 제약조건
- 위와 같은 기능들은 단지 DDL을 자동 생성할 때만 사용되고 **JPA의 실행 로직에는 영향을 주지 않음**
- 애플리케이션 개발자가 엔티티만 보고도 손쉽게 다양한 제약조건을 파악할 수 있다는 장점이 있음

---

**6. 기본 키 매핑**

`JPA가 제공하는 데이터베이스 기본 키 생성 전략`
- **직접 할당** : 기본 키를 애플리케이션에서 직접 할당, `@Id` 사용
- **자동 생성** : 대리 키 사용 방식, `@Id`에 `@GeneratedValue` 추가 후 원하는 전략 선택
> - IDENTITY : 기본 키 생성을 DB에 위임
> - SEQUENCE : DB 시퀀스를 사용해서 기본 키 할당
> - TABLE : 키 생성 테이블 사용

1. 기본 키 직접 할당 전략
- `@Id`로 매핑
- `@Id` 적용 가능 자바 타입
> - 자바 기본형
> - 자바 래퍼(Wrapper)형
> - String
> - java.util.Date
> - java.sql.Date
> - java.match.BigDecimal
> - java.match.BigInteger

- `em.persist()`로 엔티티를 저장하기 전 애플리케이션에서 기본 키를 직접 할당하는 방법

2. IDENTITY 전략
- 기본 키 생성을 DB에 위임
- `AUTO_INCREMENT`처럼 DB에 값을 저장하고 나서야 기본 키 값을 구할 수 있을 때 사용
- `@GeneratedValue(strategy = GenerationType.IDENTITY)`
- 이 전략 사용 시 JPA는 기본 키 값을 얻어오기 위해 DB를 추가로 조회\
→ `em.persist()`를 호출해서 엔티티를 저장하는 시점에 DB가 생성한 값을 JPA가 조회

3. SEQUENCE 전력
- DB의 시퀀스 : 유일한 값을 순서대로 생성하는 DB 오브젝트
- 이 시퀀스를 사용해서 기본 키 생성
- 시퀀스 생성기 등록 : `@SequenceGenerator`
- `@GenerateValue(strategy = GenerationType.SEQUENCE, generator = "(위에서 생성한 시퀀스 생성기 이름)")`
- `em.persist()`를 호출할 때 먼저 DB 스퀀스를 사용해서 식별자 조회 → 조회한 식별자를 엔티티에 할당\
→ 영속성 컨텍스트에 저장 → 플러시가 일어나면 엔티티를 DB에 저장

4. TABLE 전략
- 키 생성 전용 테이블을 하나 만들고 여기에 이름과 값으로 사용할 컬럼을 만들어 DB 시퀀스를 흉내냄
- 먼저 키 생성 용도로 사용할 테이블을 만들어야 함
- 테이블 키 생성기 등록 : `@TableGenerator`
- `@GeneratedValue(strategy = GenerationType.TABLE, generator = "(위에서 생성한 테이블 키 생성기 이름)")`

5. AUTO 전략
- 선택한 DB 방언에 따라 IDENTITY, SEQUENCE, TABLE 전략 중 하나 자동 선택
- `@GeneratedValue(strategy = GenerationType.AUTO)`
- `@GeneratedValue.strategy`의 기본값은 AUTO이므로 `@GeneratedValue`라고만 사용해도 됨
- DB를 변경해도 코드를 수정할 필요가 없다는 장점이 있음

---

**7. 필드와 컬럼 매핑: 레퍼런스**
1. @Column
- 컬럼 매핑 (객체 필드를 테이블 컬럼에 매핑)
- 속성 중 `name`, `nullable`을 주로 사용
- `insertable`과 `updatable`은 정보를 읽기만 하고 실수로 변경하는 것을 방지하고 싶을 때 사용

2. @Enumerated
- 자바의 enum 타입 매핑
- `@Enumerated` 사용

3. @Temporal
- 날짜 타입 매핑 : java.util.Date, java.util.Calendar
- 자바의 Date와 가장 유사한 timestamp로 정의

4. @Lob
- BLOB, CLOB 타입 매핑
- 지정할 수 있는 속성이 없음
- 문자면 CLOB, 나머지는 BLOB
- `CLOB` : String, char[], java.sql.CLOB
- `BLOB` : byte[], java.sql.BLOB

5. @Transient
- 특정 필드를 DB에 매핑하지 않음
- DB에 저장하지 않고 조회하지도 않음
- 객체에 임시로 어떤 값을 보관하고 싶을 때 사용

6. @Access
- JPA가 엔티티에 접근하는 방식 지정
- **필드 접근** : `AccessType.FIELD`로 지정, 필드 접근 권한이 private이어도 접근 가능
- **프로퍼티 접근** : `AccessType.PROPERTY`로 지정, 접근자(Getter) 사용
