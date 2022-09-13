3강

테이블 중심 -> 객체중심
DB에 맞는 적잘한 DDL 생성
이렇게 생성된 DDL은 개 장비에서만 사용
생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용
hibernate.hbm2ddl.auto
create : 기존테이블 삭제 후 다시 생(DROP + CREATE)
create-drop : create 와 같으나 종료시점에 테이블 DROP
update : 변경분만 반영 (운영 DB에는 사용하면 안됨)
validate : 엔티티와 테이블이 정상 매핑되었는지 확인
none : 사용하지 않음
운영장비에는 절대 create, create-drop, update 사용하면 안된다.

매핑 어노테이션
@Column (DB의 컬럼이름)
가장많이 사용됨
name : 필드와 매핑할 테이블의 컬럼이름
insertalbe, updatalbe : 읽기전용
nullalbe : null 허용여부 결정 , DDL 생성 시 사용
unique : 유니크 제약조건, DDL 생성 시 사용
columnDefinition, length, precision, scale(DDL)

@Temporal (시간관련)
날짜 타입 매핑
@Temporal(TemporalType.DATE)
localDateTime 써도됨

@Enumerated
현업에서는 꼭 String을 써야함 (EnumType.STRING). 기본은 ORDINAL(순서임))
@Lob
CLOB, BLOB 매핑
CLOB : String, char[], java.sql.CLOB
BLOB : byte[], java.sql.BLOB
이 어노테이션을 String에 쓰면 CLOB, byte에 쓰면 BLOB이 됨
@Transient
이 필드는 매핑하지 않는다.
애플리케이션에서 DB에 저장하지 않는 필드
식별자 매핑 어노테이션
@Id (직접)
IDENTITY : 데이터베이스에 위임. MYSQL
SEQUENCE : 데이터베이스 스퀀스 오브젝트 사용. ORACLE
@SequenceGenerator
TABLE : 키 생성용 테이블 사용, 모든 DB에서 사용
@TableGenerator 필요
AUTO : 방언에 따라 자동 지정, 기본 값
@GeneratedValue
권장하는 식별자
기본 키 제약조건 : null 아님, 유일, 변하면 안된다.
미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. 대리키(대체키)를 사용하자.