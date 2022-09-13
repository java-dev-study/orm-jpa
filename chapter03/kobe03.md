# 영속성 관리

### JPA에서 가장 중요한 2가지

- 객체와 관계형 데이터베이스 매핑하기 (ORM)
- **영속성 컨텍스트**

### 영속성 컨텍스트?

- JPA를 이해하는데 가장 중요한 용어
- “엔티티를 영구 저장하는 환경”
- **EntityManger.persist(entity); →** 
DB에 저장하는 것이 아니라 persist() 메소드를 통해서 Entity를 영속성 컨텍스트에 저장
- 영속성 컨텍스트는 눈에 보이지 않은 논리적인 개념이다.
- 엔티티 매니저를 통해서 영속성 컨텍스트에 접근한다.

### 엔티티의 생명주기

- **비영속**
member 객체를 생성하고, 아무것도 넣지 않은 상태.

```jsx
Member member = new Member();
member.setID(1);
member.setUsername("이범기");
```

- **영속**
    
    persist() 메소드를 이용해서 영속성 컨텍스트에 넣은 상태
    
    ```jsx
    Member member = new Member();
    member.setID(1);
    member.setUsername("이범기");
    em.persist(member) //영속 상태 -> 영속성 컨텍스트를 통해 관리된다.
    // 이때 DB에 저장되는 것이 아니다. 
    // 이후 커밋할 때 영속성 컨텍스트에 있는 것이 DB 추가.
    ```
    

- **준영속**
    
    em.detach() → 다시 영속성 상태에서 지운다.
    
- **삭제**

        em.remove() → DB 에서 지운다.

### 영속성 컨텍스트 특징

**엔티티 조회, 1차 캐시**

 영속성 컨텍스트는 내부에 캐시를 가지거 있는데 이것을 1차 캐시라고 한다. 영속성 컨텍스트 내부에 Map이 하나 있는데 키는 @Id로 매핑한 식별자, 값은 엔티티 인스턴스.

**데이터베이스에서 조회**

  만약 em.find()에서 엔티티를 호출했는데, 영속성 컨텍스트에 존재하지 않는다면, 1차로 캐시에 저장한 이후에 영속 상태의 엔티티를 반환한다.

**영속 엔티티의 동일성 보장**

```jsx
Member member1 = em.find(Member.class, "member1");
Member member2 = em.find(Member.class, "member1");
****//영속성 컨텍스트는 성능상 이점과 엔티티의 동일성을 보장한다.
```

**엔티티 등록 - 트랜잭션을 지원하는 쓰기 지연**

```jsx
em.persist(member1);  
em.persist(member2);   ///여기까지는 DB에 보내지 않는다.

transaction.commit();  ///트랜잭션 커밋
// 두개를 저장하려고 할 때 캐시를 이용해서 한 번에 DB 등록가능.
```

**엔티티 수정 - 변경 감지**

```jsx
memberA.setUsername("hi");
memberA.setAge(20);
//em.update(member) -> 이런 코드 없이 수정이 가능한 이유는
//JPA는 스냅샷(최초 상태)를 유지해서 변경된 것이 있다면 변경된 엔티티를 찾는다.
```

- 변경 된 엔티티가 있으면 수정 쿼리를 생성해서 쓰기 지연 SQL 저장소에 보낸 후 SQL을 DB에 보내고 커밋한다.

**엔티티 삭제** 

엔티티 삭제는 엔티티 등록과 비슷하게 쓰기 지연 SQL 저장소에 등록 이후 트랜잭션 커밋한다.

### 플러시

**플러시는 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다.**

- 변경 감지 , 수정된 엔티티를 쓰기 지연 SQL 저장소에 등록, 
쓰기 지연 SQL 저장소의 쿼리를 DB에 전송
- 영속성 컨텍스트를 플러시하는 방법
    
    em.flush(), 트랜잭션 커밋, JPQL 쿼리 실행.
    
    // 웬만하면 플러시 모드는 Auto (default로 설정)
    
- 플러시는 영속성 컨텍스트를 비우지 않음
- 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화
- 트랜잭션이라는 작업 단위가 중요 → 커밋 직전에만 동기화 하면 된다.

### 준영속 상태

- 영속 → 준영속
- 영속 상태의 엔티티가 영속성 컨텍스트에서 분리될 때 (detached)
- 영속성 컨텍스타가 제공하는 기능을 사용 못함
- 준영속 상태 만드는 방법
1. em.detach() → 특정 엔티티만 준영속
2. em.clear()  → 영속성 컨텍스트를 완전히 초기화
3. em.close() → 영속성 컨텍스트를 종료한다.