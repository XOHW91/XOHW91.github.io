---
layout: single
title:  "JPA mechanism"
---

<script src="https://gist.github.com/XOHW91/9e3e52de6b91ca4ac39c601a03c9032d.js"></script>

1. ###  EntityManagerFactory

   - JPA에서 가장 중요한 인터페이스 중 하나이며, 애플리케이션의 생명주기동안 단 한번만 생성이 되어야한다.

   - 이 인터페이스는  EntityManager를 생성해서 애플리케이션에서 데이터베이스와의 연결을 설정하고, 필요한 영속성 컨텍스트를 생성하는 역할을 함.

     

2. ###  EntityManager

   - 트랜잭션 단위의 작업을 할 때는 EntityManager를 사용하게 되는데 EntityManager를 생성했다는 건 데이터베이스 서버와 애플리케이션 간의 연결인 DB커넥션을 받았다고 생각하면 된다. 

   - 트랜잭션 : 데이터베이스에서 한 번에 수행되어야 할 일련의 연산들을 나타내는 논리적인 작업 단위

     

3. ###  EntityTransaction

   - 트랜잭션을 관리하기 위한 인터페이스이며 호출하면 트랜잭션을 얻을 수 있다.
   - 트랜잭션을 얻는다는 말은 트랜잭션을 시작하기 위해 필요한 객체를 얻는 다는 것을 의미하며, 트랜 잭션은 여러 데이터베이스 연산을 원자적으로 묶어주기 위해 필요한 개념이다. (이를 관리하기 위해 EntityTransaction 객체를 사용하는 것.)
   - 즉, 트랜잭션을 시작하려면 이 객체를 얻어와야하고, 그 후에 트랜잭션 관련 작업을 수행할 수 있다.
   - 트랜잭션을 시작하고, 커밋하거나 롤백하는 등의 트랜잭션 조작 메서드를 제공하며, 트랜잭션을 시작하려면 tx.begin();을 하면됨.  





<script src="https://gist.github.com/XOHW91/5d580bd5f697bda20bafad81231b8230.js"></script>

4. ### Entity 

   - 데이터베이스 테이블과 매핑되는 자바의 '객체'를 의미함. 

   - 기본 생성자를 반드시 써주어야 한다.

     - 리플렉션 / 프록시 객체 생성 / 인스턴스 초기화 할 때 기본 생성자가 반드시 필요하다.

       

   - @Entity : 해당 어노테이션을 사용하여 클래스를 엔티티로 지정하면 JPA는 클래스를 데이터베이스 테이블과 매핑하고, 영속성 컨텍스트에서 관리할 수 있도록 함.   

     - 테이블 명이 member인 경우 class 명명규칙에 따라 클래스명을 Member로 설정하면 자동매핑 됨.

     - 테이블 명(users)과 class명(Member)이 다른 경우 @Entity(name = "users")를 사용

       

   - @Id : JPA 엔티티 클래스에서 기본키(PK)필드를 나타냄.

     

     

   
