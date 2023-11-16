---
layout: single
title:  "JPA PersistenceContext"
typora-root-url: ../
---



1. ###  Member INSERT

![20231027_173437](/images/2023-10-27-jpa-PersistenceContext/20231027_173437-1698396336988-2.jpg)

- JPA를 사용하여 데이터베이스에 새로운 Member 객체를 삽입하는 작업



2. ###  조회

<script src="https://gist.github.com/XOHW91/9f276cbf37331bafc5b1ac484442d6aa.js"></script>

3. ### PersistenceContext (영속성 컨텍스트)
   
   - "엔티티를 영구 저장하는 환경"
   
   - 엔티티 매니저를 통해서 영속성 컨텍스트에 접근
   
     

```
// 비영속
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");
```

- 객체를 생성만 한 상태

- 영속성 컨텍스트에 의해서 관리가 되지 않는 상태를 말한다.

  

```
// 영속
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

// 여기서 영속화
em.persist(member);
```

- EntityManager의 트랜잭션을 시작하고, persist(member) 해줬을 때가 영속화 된 시점.

- 즉, 이때부터는 영속성 컨텍스트에 의해 관리가 가능해 진다.

  

```
// 준영속
em.detach(member);

//삭제
em.remove(member);
```

- 준영속 : 회원 엔티티를 영속성 컨텍스트에서 분리한 상태
- 삭제 : 객체를 삭제한 상태





4. ###  PersistenceContext (영속성 컨텍스트)의 이점

```
Member member = new Member();
member.setId("member1");
member.setUsename("회원1");

// 1차 캐시에 저장 됨.
em.persist(member);

// 1차 캐시에서 조회
Member findMember = em.find(Member.class, "member1");

// 1차 캐시에 없는 객체 조회
Member findMember2 = em.find(Member.class, "member2");
```



- 1차 캐시 

영속성 컨텍스트 내부에는 1차캐시라는 것이 존재하는데, 엔터티 객체들을 저장하고 관리하는 곳이다.

이 캐시는 엔터티의 식별자를 키로 사용하여 엔터티를 저장하고 관리하며, em.persist(member);와 같이 엔터티를 저장하면 해당 엔터티는 1차 캐시에 저장된다.

때문에 em.find(Member.class, "member1");를 하게 되면 1차 캐시에서 캐시값을 조회해서 member 객체를 찾아옴. 



![image-20231116084121000](/images/2023-10-27-jpa-PersistenceContext/image-20231116084121000.png)



1차 캐시에 없는 데이터에 대해서는 DB에서 조회 > 1차 캐시에 저장 > 객체 반환.





- 영속 엔티티의 동일성 보장

  ```
  Member a = em.find(Member.class, "member1");
  Member b = em.find(Member.class, "member2");
  
  System.out.println(a == b); 
  ```

영속성 컨텍스트 내에서는 특정 식별자를 가진 엔티티를 저장, 관리하기 때문에 여러 번 조회하더라도 같은 객체가 반환된다.





- 엔티티 등록 - 트랜잭션을 지원하는 "쓰기 지연 SQL 저장소"

```
// 영속
Member memberA = new Member();
member.setId("memberA");

Member memberB = new Member();
member.setId("memberB");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

// 여기서 영속화
em.persist(memberA);
em.persist(memberB);

// 트랜잭션 커밋 - 여기서 insert 쿼리 실행됨.
transaction.commit(); 
```



> em.persist()를 하게 되면 1차 캐시에는 저장된 상태이지만 INSERT SQL은 생성만 하고, 쓰기지연 SQL 저장소에 저장을 해 둠.



이후, `transacion.commt();` 을 하는 순간 그제서야 쓰기 지연 SQL 저장소에 있던 쿼리들을 flush(); > 데이터 베이스에서의 commit(); 이 실행 됨.

데이터베이스에서의 커밋까지 실행되어야지  DB에 데이터가 영구적으로 반영이 되는 것. 



- 변경감지 (Dirty Checking)

  

```
EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

Member memberA = em.find(Member.class, "memberA");
memberA.setUsername("hi"); // 값을 변경한 경우?

//em.update()가 필요할까? 아니다. em.flush()면 충분함
em.flush();

transaction.commit();
```

영속성 컨텍스트 내에는 엔터티의 상태를 추적하는 `Snapshot`이라는 기능을 가진다.

스냅샷은 `em.persist(member)`와 같은 영속성 컨텍스트에 새로운 엔터티를 추가할 때 또는 `em.find(Member.class, memberId)`와 같이 엔터티를 조회할 때 생성되며 트랜잭션 커밋될 때의 엔티티와 스냅샷을 비교해 변경여부를 확인하고 변경된 부분만을 update해주는 쿼리를 작성한다.



만약 em.flush()를 하지 않고 em.persist(memberA);를 하게 되면, update 쿼리가 쓰기 지연 SQL 저장소에 저장이 됨 > 이후 commit()할 때 쿼리가 실행, 일반적으로 트랜잭션이 커밋될 때 자동으로 flush()가 호출되기 때문에 적어주지 않아도 됨.

em.flush()를 명시적으로 적어 주게 되면 특정 엔티티에 대해서만 수행되게 되며, flush()를 호출할 때 변경된 내용이 데이터베이스에 적용되며, 이후에 `em.commit()`을 호출하면 트랜잭션이 커밋되면서 변경 내용이 영구적으로 반영된다.



`em.flush()`를 호출하면 영속성 컨텍스트의 변경 내용이 데이터베이스에 동기화된 상태(커밋은 안된 상태). 이때 쓰기 지연 SQL 저장소에 있는 쿼리들도 함께 동기화 처리된다.  변경 내용을 영구적으로 데이터베이스에 반영하려면 이후에 `em.commit()`을 호출하여 트랜잭션을 커밋하면 됨. 



즉, 영속성 컨텍스트를 플러시 하는 방법은 아래와 같다.

> - em.flush() 호출
> - 트랜잭션 커밋
> - JPQL 쿼리 실행 

















