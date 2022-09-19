### 4. 엔티티 매핑 
- 객체와 테이블 매핑 : @Entity , @Table
- 기본키 매핑 : @Id
- 필드와 컬럼 매핑 : @Column


#### 4.1 @Entity 
- JPA 가 관리 
- 기본 생성자 필수 
- final , enum , interface , inner 클래스 사용 불가 
- 필드에 final 불가 

#### 4.2 @Table
- 엔티티와 테이블 매핑 시 사용 

#### 4.3 데이터베이스 스키마 자동 생성
- xml 의 hibernate.hbm2ddl.auto 추가 
- 운영되고 있는 개발환경에서는 적용 어려움. 매핑 참고용 정도로 파악 

#### 4.4 DDL 생성 기능
- NOT NULL , LENGTH , Unique 제약조건 등 DDL 생성 시에만 사용
- JPA 실행로직에는 영향 없음 

#### 4.6 기본 키 매핑 
- 키 생성 시, hibernate.id.new_generator_mappings=true 속성 지정 필수 
- @Id 에 @GeneratedValue 추가 
- 직접할당 : test.setId("id1")
- 자동 생성 
  -- IDENTITY : 데이터 베이스에 insert 후 기본 키값 조회 가능 (@GeneratedValue 의 GenerationType.IDENTITY)
  -- SEQUENCE : 데이터 베이스에서 식별자조회 후, 엔티티에 할당 및 영속 컨택스트에 저장 (@SequenceGenerator)
  -- TABLE : 키 생성 전용 테이블 생성 후 매핑 (@TableGenerator)
- AUTO 전략 : 
