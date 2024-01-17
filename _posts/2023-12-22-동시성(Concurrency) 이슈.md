---
layout: single
title:  "동시성(Concurrency) 이슈"
---



>회사 업무 중 마주한 동시성 이슈.
>
>동시성 이슈를 다루기 위해서는 자바의 프로세스와 스레드에 대한 기본적인 이해가 필요하기 때문에
>
>프로세스와, 스레드, 동시성 이슈를 해결하는 방법을 정리해보기로 함.

------



## 자바의 프로세스와 스레드

`Process`는 운영 체제에서 실행 중인 독립적인 프로그램 단위이며, 각 프로세스는 자체의 메모리 공간과 자원을 가지고, 서로 독립적으로 실행된다. ` Thread`는 프로세스 내에서 실행되는 경량의 실행단위이다. 하나의 프로세스는 여러 개의 스레드를 가질 수 있으며, 스레드는 프로세스의 자원을 공유하고, 동시에 실행될 수 있다.



일반적으로 하나의 웹 애플리케이션은 하나의 프로세스 내에서 실행된다. 

웹 애플리케이션은 사용자 요청을 처리하기 위해 서버에서 실행되는 프로그램이다.  하나의 프로세스 내에서는 여러 스레드로 동작할 수 있으며, 각 스레드는 동시에 여러 요청을 처리할 수 있기 때문에 이로 인해 동시성 문제가 발생할 수 있다. 

다시 말해, 프로세스는 웹 애플리케이션을 실행하는 운영체제의 단위이며, 웹 애플리케이션 내에서는 다수의 스레드가 동작하여 다수의 클라이언트 요청을 동시에 처리한다. 이러한 특징때문에 **일반적으로 동시성 문제는 여러 스레드가 공유 자원에 동시에 접근할 때 발생한다. **

 

> 회사 업무 중 마주한 상황은 아래와 같았다.
>
> 1. "섭외"버튼을 누르면 섭외 대기 중인 사람들의 목록과 섭외 대상 목록이 넘어옴.
>
> 2. 서버에서 조건에 맞는 섭외 대기자를 판별 후 섭외 처리를 자동으로 하는 로직
>
>     ==> 이 로직이 "공유자원"
>
> 3. 섭외 대기중인 사람들과 섭외 대상에 대한 데이터들은 DB에 **update**됨. 
>
>    ==> "섭외"버튼을 동시에 누를 경우 덮어쓰기 등의 문제가 존재함. "동시성 이슈"
>
> 4. 이 버튼은 "관리자"만 누를 수 있으나 관리자가 여러 명이기때문에 동시에 "섭외"버튼을 누르게 되는 상황 가능성이 높음. 
>
>    ==> 여러 명의 관리자 = 다수의 스레드





## 동시성 이슈 해결방안

알아본 바 해결방안은 크게 4가지이다. 



- #### **MySQL InnoDB Lock**

  MySQL의 InnoDB 엔진을 사용하여 특정 레코드나 테이블에 락을 걸어서 동시에 여러 트랜잭션이 해당 자원에 접근하기 못하게 하는 방법이다. 트랜잭션의 `순차성`을 보장해주기 위한 방법 중 하나이며, 데이터의 일관성도 보장해준다.

  ex) 콘서트 예매에서 하나 남은 티켓을 두 명의 고객이 사기 위해 동시에 접근했다면 한 명에게만 판매되도록 보장해야 할 때

  > 하지만 이는 데이터베이스 레벨에서의 동시성 문제를 해결하는 방법. 
  >
  > 섭외"의 경우에는 메모리에 저장해둔 섭외된 사람의 목록만큼 update쿼리를 날려야 했고,
  > 트랜잭션 처리 전에 이미 for문과 조건절이 많았기 때문에 이 경우에는 적용하기 무리라 판단.
  >
  > 데이터베이스 레벨이 아닌 애플리케이션 레벨에서 문제를 해결하는 방법을 택하기로 함.

- #### **Java Synchronized**

  java의 Synchronized method는 클래스 인스턴스에 lock을 건다.

  아래의 예제를 통해 설명해보면 

  ```
  public class Main {
  
      public static void main(String[] args) {
   		EX ex = new Ex();
   		
          Thread A = new Thread(() -> {
          	ex.getList("A");
          });
          
          Thread B = new Thread(() -> {
          	ex.getList("B");
          });
          
          A.start();
          B.start();
      }
  }    
  ```

  ```
  public class EX {
  	public synchronized void getList(String id) {
  		System.out.println(id + "enter");
  		try {
  			Thread.sleep(10000);
  		} catch(InterruptedException e) {
  			e.printStackTrace();
  		}
  		System.out.println(id + "exit");
  	}
  }
  ```

  ```
  출력 결과는 
  
  A enter
  A exit
  B enter
  A exit
  ```

  즉, 접근한 순서대로 각각의 스레드가 인스턴스에 접근하게 된다.

  

  만일 아래처럼 각각의 스레드가 각각의 인스턴스에 접근하게 되는 경우에는 lock을 공유하지 않기 때문에

  ```
  public class Main {
  
      public static void main(String[] args) {
   		EX ex1 = new Ex();
   		EX ex2 = new Ex();
   		
          Thread A = new Thread(() -> {
          	ex1.getList("A");
          });
          
          Thread B = new Thread(() -> {
          	ex2.getList("B");
          });
          
          A.start();
          B.start();
      }
  }    
  ```

  ```
  출력 결과는
  A enter
  B enter
  A exit
  B exit
  ```

  

  더 확실하게 하기 위해 하나의 예시를 추가해보자면

  ```
  public class Main {
  
      public static void main(String[] args) {
   		EX ex = new Ex();
   		
          Thread A = new Thread(() -> {
          	ex.getList("A");
          });
          
          Thread B = new Thread(() -> {
          	ex.showList("B");
          });
          
          A.start();
          Thread.sleep(1000);
          B.start();
      }
  }    
  ```

  ```
  public class EX {
  	public void showList(String id) {
  		System.out.println(id + "another enter");
  	}
  	public synchronized void getList(String id) {
  		System.out.println(id + "enter");
  		try {
  			Thread.sleep(10000);
  		} catch(InterruptedException e) {
  			e.printStackTrace();
  		}
  		System.out.println(id + "exit");
  	}
  }
  ```

  ```
  출력 결과는
  A enter
  B another enter
  A exit
  ```

  A는 실행과 동시에 lock이 되고, A가 진행되는 도중에 B에서 showList()를 호출

  즉, 인스턴스 접근 자체에 lock이 된 것은 아니다.

  하지만, showList()에도 synchronized 키워드를 붙이게 된다면 synchronized가 적용된 메서드끼리 lock 을 공유하기때문에 

  출력 결과는 `A enter > A exit > B another enter`이다.

  

  주의할 점은  아래와같이 메서드에 static이 붙으면 

  ```
  public class EX {
  	public static synchronized void getList(String id) {
  		System.out.println(id + "enter");
  		try {
  			Thread.sleep(10000);
  		} catch(InterruptedException e) {
  			e.printStackTrace();
  		}
  		System.out.println(id + "exit");
  	}
  }
  ```

  아래와같이 각각의 인스턴스에 접근해도 lock이 발생한다. 

  lock이 클래스 수준에서 걸리게 되면서 클래스 수준에서 동기화되기 때문이다.

  즉, A가 먼저 lock을 획득하게 되면 B는 lock을 얻을 때까지 대기하게 된다. 이 lock은 클래스에 대한 것이기 때문에

  클래스의 모든 인스턴스에 대한 lock을 가지게 되는 것이다.

  ```
  public class Main {
  
      public static void main(String[] args) {
   		EX ex1 = new Ex();
   		EX ex2 = new Ex();
   		
          Thread A = new Thread(() -> {
          	ex1.getList("A");
          });
          
          Thread B = new Thread(() -> {
          	ex2.getList("B");
          });
          
          A.start();
          B.start();
      }
  }    
  ```

  

- #### **Flag 사용**

   회사에서 마주한 동시성 문제는 동시에 "섭외"버튼을 누르는 경우가 많아봤자 동시인원 10명 내외라서 

   간단하게 구현할 수 있는 Flag를 사용하기로 했다.

   Global class에 아래처럼 멤버변수인 Flag를 static으로 선언함으로써 ServiceImpl의 "섭외" 로직이 실행되는 시점에서 
   Global 객체 생성 없이 멤버변수를 사용함. static으로 선언한 것은 해당 클래스의 모든 인스턴스가 동일한 값을 공유해야하기 때문이다.

  ```
  public static boolean Flag = false;
  ```

  실행되는 시점에 `Flag=ture;` / 종료되는 시점에 `Flag=false;`

  각각 다른 스레드인 A와 B가 "섭외" 로직에 접근하더라도 먼저 실행된 A스레드가 "섭외"를 진행 중(Flag=true)이라면

  B는 A가 로직을 다 실행할 때까지 "섭외"로직에 접근하지 못함.         	
