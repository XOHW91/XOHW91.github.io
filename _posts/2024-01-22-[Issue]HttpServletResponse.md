---
layout: single
title:  "[Issue] HttpServletResponse"
typora-root-url: ../
---



# ✏️ HttpServletResponse ?

`HttpServletResponse`는 서블릿 컨테이너에서 클라이언트로부터의 HTTP 요청에 대한 응답을 생성하는 데 사용되는 객체이다. 클라이언트로 데이터를 보내거나, 응답 헤더를 설정하거나, 리다이렉션을 수행하는 데에 활용된다. 



#### **알아야 할 주요 개념** 

- **응답 생성 및 전송 :**  `HttpServletResponse`는 클라이언트로 응답을 생성하고 전송하는 데 사용되는데 `getWriter()`나 `getOutputStream()` 메서드를 통해 응답 바디를 작성할 수 있다. 

  ==> 응답 바디를 작성한다는 것은 클라이언트에게 전송될 웹 페이지의 실제 내용이나 데이터를 생성하는 것을 의미한다. HTTP 응답은 헤더와 바디로 이루어져 있으며, 헤더는 응답에 대한 메타 정보를, 바디는 실제 데이터를 포함한다. 

  ```
  protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      // 응답 헤더 설정
      response.setContentType("text/plain");
  
      // 응답 바디 작성
      PrintWriter writer = response.getWriter();
      writer.println("Hello, World!");
  }
  ```

​	==> 클라이언트가 이 서블릿에 HTTP GET 요청을 하면, 서버는 "Hello, World!"를 응답으로 전송하게 된다.



- **응답 헤더 설정 **: 클라이언트로 전송되는 응답에는 헤더 정보가 포함되는데  `setHeader()` 메서드를 사용하여 응답 헤더를 설정할 수 있다.

  ```
  response.setHeader("Content-Type", "text/html");
  ```



- **상태 코드 설정** : 응답의 상태 코드는 클라이언트에게 요청의 성공 또는 실패를 나타낸다. `setStatus()` 메서드를 사용하여 상태 코드를 설정할 수 있다.

  ```
  response.setStatus(HttpServletResponse.SC_OK); // 200 OK
  ```

  

- **리다이렉션** : 클라이언트를 다른 URL로 리다이렉트하는 경우에도 `HttpServletResponse`가 사용된다.

  ```
  response.sendRedirect("/new-page");
  ```



- **쿠키 추가 **: `HttpServletResponse`를 사용하여 클라이언트에게 쿠키를 추가할 수 있다.



- **캐싱 제어** : `Cache-Control` 헤더 등을 설정하여 클라이언트 및 프록시 서버에서 응답 캐싱을 제어할 수 있다.







## 💭 **ISSUE**

아래 이슈때문에 HttpServletResponse에 대해 정확히 짚고 넘어가려고 이 글을 작성했다. 

```
public class MemberService implements UserDetailsService {
    private final MemberRepository memberRepository;
    private final JwtTokenService jwtTokenService; 
    private final HttpServletResponse response;
}
```

해당 로직은 내가 실무하면서 MemberService 내에서 로그인 처리하면서 작성한 것이다.
로그인에 성공하면 jwt토큰을 발급하고 쿠키에 저장하는 방식으로 로직을 작성했는데
문제는 `HttpServletResponse`를 전역변수에 선언하는 짓(?)을 해버려서 수정을 하게 됐다.

**`HttpServletResponse`** **를 왜 전역변수에 선언하면 안될까?**
이를 이해하려면 아래 객체지향 프로그래밍의 기본 원칙 중 하나인 "의존성 주입"에 관한 내용을 먼저 이해해야 한다.

 의존성 주입은 클래스가 직접 필요로 하는 의존성(다른 클래스나 객체에 대한 의존)을 외부에서 주입받도록 하는 디자인 패턴 중 하나이다. 이 패턴을 통해 클래스 간의 결합도를 낮추고 유연한 코드를 작성할 수 있는데, `HttpServletResponse`를 전역변수로 선언하는 것은 해당 클래스가 내부적으로 자신이 필요로 하는 의존성을 직접 생성하고 유지하는 것이며, 이는 의존성 주입 원칙을 위배하는 것이다. 
대신, 해당 클래스가 `HttpServletResponse`를 필요로 하는 메서드나 생성자를 통해 외부에서 주입받도록 하는 것이 권장된다. 위 로직에서처럼 전역변수로 선언하여 전역 상태를 가지는 것은 다중 스레드 환경에서 예기치 못한 동작을 초래할 수 있기 때문에 특히 웹 애플리케이션과 같이 다수의 동시 요청을 처리하는 환경에서는 신중해져야 한다. 이러한 이유로 의존성 주입을 통해 필요한 의존성을 명시적으로 주입받도록 하는 것이 바람직 함.

**`또한, ` **HttpServletResponse를 전역 변수로 선언하게 되면 해당 객체의 상태를 여러 메서드에서 공유하게 되고, 이는 다중 스레드 환경에서 예기치 못한 동작을 초래할 수 있다. 때문에 필요한 메서드에 매개변수로 전달하는 방식이 더 나은 설계 방식이다.



**나같은 경우는 response를 사용하는 메서드의 파라미터에 HttpServletResponse를 선언해서 사용하도록 수정했다.**