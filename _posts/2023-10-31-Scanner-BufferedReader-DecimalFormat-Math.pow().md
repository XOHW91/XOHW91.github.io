---
layout: single
title:  "[Java] Scanner / BufferedReader / DecimalFormat / Math.pow()"
typora-root-url: ../
---

<script src="https://gist.github.com/XOHW91/e7fddd19ee7359ae7243c21684b56b4d.js"></script>

### 1. Scanner

자바의 클래스 중 하나로 키보드로 값을 입력받을 때 사용하며,  ** **import java.util.Scanner **** import를 반드시 해주어야 함.

`Scanner` 객체를 생성할 때  ** **System.in** **을 넣어주게 되는데 이는 사용자로부터 입력을 받기 위한 입력 스트림이다.



- 위의 예시에서는 sc.nextInt()로 int 타입의 값을 받았지만 다양한 타입의 값을 입력받을 수 있다.

​        *sc.nextDouble() / sc.nextFloat() / sc.nextBoolean() / sc.nextLong()*

- 문자열로 받고싶을 때는 *sc.next() / sc.nextLine()* 을 사용하면 되는데 둘의 **차이점**이 있다.

  - sc.next() : 공백을 기준으로 한 단어씩 끊어서 읽어 들임.

  - sc.nextLine() : 한 줄에 입력된 한 문장을 읽어 들임.

  ```java
  String A = sc.next(); 
  
  String B = sc.next(); 
  
  A = Hello / B = World
  ```

  만약, "Hello World"를 입력한 경우 A와B에는 공백을 기준으로 한 단어씩 들어가 있음.

   

  ```java
  String C = sc.nextLine();
  
  // "Hello World"를 입력한 경우
  C = "Hello World" 가 들어가 있음. 
  ```

  

### 2. System.in 

System.in은 키보드 입력값을 받아오기 위해 객체를 사용할 때 필요한 입력 스트림이다. 

- 스트림이란 A에서 B로의 데이터 흐름을 말하는데 단방향이기 때문에 입력과 출력이 동시에 발생할  수 없어 입력 스트림, 출력 스트림으로 나뉜다. 

  가장 기본이 되는 입력 스트림은 **InputStream** 이며, System.in은 바이트 단위로 데이터를 읽어오는 InputStream 타입의 필드다. 

   System의 in 변수를 사용하면 콘솔이나 명령줄을 통해 값을 입력받을수 있다. 

  - `Scanner` 클래스의 생성자 `new Scanner(System.in)`을 호출하면, `Scanner` 객체는 `System.in`으로부터 바이트 스트림을 읽어들이고, 이를 문자열이나 기본 자료형 값으로 파싱(parsing)하여 사용자에게 쉽게 다룰 수 있는 형태로 제공함.

  - Scanner 클래스 파일을 살펴보면 생성자 오버로딩이 되어있는데  new Scanner(System.in)을 하게 되면 이 오버로딩된 생성자에 의해서 

    문자를 온전하게 입력받을 수 있는 문자 스트림인 inputStreamReader 을 생성하게 된다. 이때, `Scanner` 클래스 내부에서는 `System.in`을 기반으로한 `InputStreamReader` 클래스를 사용하여 바이트 스트림을 문자 스트림으로 변환하고, 이를 다시 `Scanner` 클래스가 읽을 수 있는 형태로 래핑하게 되고, 이러한 과정을 통해 문자열이나 기본 자료형 값을 읽어올 수 있게 된다. 



<script src="https://gist.github.com/XOHW91/a8e10cff5d0894fb6a21c4f5dd194bfc.js"></script>

### 3. BufferedReader

`BufferedReader`도 Java의 입출력 기능을 사용하여 데이터를 읽어오는 클래스 중 하나이다.

BufferedReader는 특히 문자 기반 입력 스트림을 버퍼링하여 읽어오는 데 사용되며, `버퍼링(buffering)은 데이터를 한 번에 읽어오는 것보다 작은 조각으로 나누어 읽어오는 방법으로, 입출력 성능을 향상시킬 수 있다.

`BufferedReader`는 `readLine()` 메서드를 통해  ** **한 줄씩** ** 데이터를 읽어올 수 있으며,  주로 파일이나 네트워크 소켓과 같은 입력 소스로부터 데이터를 읽어오는 데 사용된다. 예를 들어, 텍스트 파일에서 한 줄씩 데이터를 읽어오거나 네트워크에서 메시지를 받아올 때 유용하게 사용될 수 있음.

- 위의 예시에서 `new InputStreamReader(System.in)`을 사용하는 이유는 `InputStreamReader` 클래스는 바이트 입력 스트림(`System.in`)을 문자 입력 스트림으로 변환해주기 때문이다. `System.in`은 표준 입력 스트림으로서 바이트 단위로 데이터를 읽어오기 때문에 파싱을 해주어야 하는데 키보드 입력을 문자로 읽어오는 것이 편리하므로, `InputStreamReader`를 사용하여 `System.in`을 문자 입력 스트림으로 변환한다.
-  즉, `BufferedReader`는 문자 입력 스트림인 `InputStreamReader`를 사용하여 데이터를 읽어오기 때문에 `new InputStreamReader(System.in)`을 `BufferedReader`의 생성자에 전달하여 사용함.



### 4. StringTokenizer

문자열을 분리하는 데 사용되는 클래스이며, 주어진 문자열을 지정된 구분자를 기준으로 `token`으로 분리할 수 있음. 

이때, 기본 구분자는 공백임. 

위의 예제에서는 `BufferedReader`를 사용해서 한 줄의 문자열을 읽어오고,  `StringTokenizer`을 사용하여 공백을 기준으로 두 개의 토큰으로 분리함. 



```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
StringTokenizer st = new StringTokenizer(br.readLine(), "_");
```

구분자를 지정하고 싶다면, new StringTokenizer() 해줄 때 지정해주면 된다.

 

### 5. DecimalFormat

`DecimalFormat`은 Java에서 숫자를 원하는 형식의 문자열로 포맷팅할 때 사용하는 클래스이며, 숫자를 형식화하여 원하는 형태로 출력하거나 문자열로 변환할 때 사용한다.  숫자 패턴을사용하여 숫자를 포맷화하게 됨.

 위의 예시에서는 소수점 아래 10자리까지만 출력하기 위해서 아래처럼 사용

```
        DecimalFormat form = new DecimalFormat("#.##########");
        System.out.println(form.format(a/b));  
```





### 6. Math.pow() - 자바 거듭 제곱 구하기

double A = Math.pow(5, 2);    // 5의 제곱을 의미

double B = Math.pow(10, -4);	// 10의 -4승을 의미 

입출력값은 모두 double형이며, Math.pow(대상숫자, 지수)를 넣어주면 됨.









** ***생각안날 땐 아래 링크들어가서 다시 공부하기*** **

https://st-lab.tistory.com/41
