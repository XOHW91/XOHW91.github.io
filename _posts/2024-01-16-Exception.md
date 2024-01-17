---
layout: single
title:  "Exception"
typora-root-url: ../

---



# ✏️예외란?

예외를 제대로 이해하기 위해선 예외와 오류의 차이점을 알아야 한다. 
`오류(error)`는 시스템 레벨에서 비정상적인 상황이 발생하는 것으로, 예측불가하므로 개발자는 오류에 대한 처리는 신경쓰지 않는다.
하지만 `예외(Exception)`는 개발자가 자신이 구현한 로직에서 예외를 예측하고, 그에 따른 예외처리를 신경써야 한다.    



#### 📖 **예외 클래스**

모든 예외 클래스는 Throwable 클래스를 상속받고있으며, Throwable을 상속받은 클래스는 다시 Error와 Exception로 나뉜다.
우리가 살펴봐야 할 건 `Exception`이며 Exception의 수많은 자식 클래스를 살펴볼 필요가 있다.
자식 클래스는 다시 Checked Exception과 Unchecked Exception으로 나눠지는데 무슨 차이인지 알아보자.

`Checked Exception` 

- 체크 예외는 try~catch로 예외를 잡아서 처리하거나 throws 예외를 필수로 선언해야 한다. 그렇지 않으면 컴파일 오류가 남

- 특징 : 

  - RuntimeException 상속을 받지 않았다.    

  - 예외 처리 코드 **필수**

  - 확인 시점은 컴파일 단계

    ex) **IOException, SQLException, ClassNotFoundException**

- 장점 : 컴파일러를 통해 문제를 잡아주기때문에 개발자가 실수로 예외를 누락하지 않도록 도와줌.

- 단점 : 개발자가 결국은 체크 예외를 반드시 잡거나 던지도록 처리해야하기 때문에 번거로움.

```
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class CheckedExceptionExample {
    public static void main(String[] args) {
        try {
            // 파일을 읽어오는 작업
            BufferedReader reader = new BufferedReader(new FileReader("example.txt"));
            String line = reader.readLine();
            System.out.println("파일 내용: " + line);
            reader.close();
        } catch (IOException e) {
            // Checked Exception인 IOException 처리
            System.err.println("파일을 읽어오는 도중 오류 발생: " + e.getMessage());
        }
    }
}
```



`Unchecked Exception (RuntimeException)`

- 런타임 예외는 컴파일 과정에서는 문제를 발견하지 못하고, 정상적으로 컴파일 된 후 프로그램 실행 중에 발생하는 오류사항들이다.
  `Unchecked Exception`은 이런 런타임 예외를 상속한다. ==> RuntimeException 상속받음.

- 예외 발생 시 런타임 중지(프로그램종료)가 된다. 예를 들면 NullException이나 Index length에 대한 오류 

```
public class UncheckedExceptionExample {
    public static void main(String[] args) {
        try {
            // 배열의 길이를 초과하여 접근하는 작업
            int[] numbers = {1, 2, 3};
            System.out.println("배열 요소: " + numbers[5]);  // 여기서 ArrayIndexOutOfBoundsException 발생
        } catch (ArrayIndexOutOfBoundsException e) {
            // Unchecked Exception인 ArrayIndexOutOfBoundsException 처리
            System.err.println("배열 인덱스 초과: " + e.getMessage());
        }
    }
}

```




일반적으로 checked Exception을 만나면 더 구체적인 Unchecked Exception을 발생시켜 정확한 정보를 전달하고 로직의 흐름을 끊는다.

```
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class ConvertCheckedToUncheckedExample {
    public static void main(String[] args) {
        try {
            readDataFromFile("example.txt");
        } catch (RuntimeException e) {
            // Unchecked Exception인 경우 처리
            System.err.println("데이터 처리 중 오류 발생: " + e.getMessage());
        }
    }

    private static void readDataFromFile(String fileName) {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader(fileName));
            String line = reader.readLine();

            // 파일 형식이 잘못되었을 때 Unchecked Exception으로 변환하여 던짐
            if (line != null && line.contains("invalidFormat")) {
                throw new RuntimeException("잘못된 파일 형식입니다.");
            }

            System.out.println("파일 내용: " + line);
        } catch (IOException e) {
            // Checked Exception인 IOException 처리
            System.err.println("파일을 읽어오는 도중 오류 발생: " + e.getMessage());
        } finally {
            try {
                if (reader != null) {
                    reader.close();
                }
            } catch (IOException e) {
                // 예외 무시 또는 로깅
                System.err.println("리더 닫기 중 오류 발생: " + e.getMessage());
            }
        }
    }
}

```

위 코드에서 `readDataFromFile` 메서드에서 파일을 읽는 도중에 예외가 발생하면, 이를 `RuntimeException`으로 변환하여 상위 메서드인 main메서드 던지고 있다. 즉, `readDataFromFile` 메서드에서 발생한 `RuntimeException`은 `main` 메서드에서 호출한 부분에서 잡히게 된다. 이로 인해 Checked Exception을 처리하다가 특정 조건에서 Unchecked Exception으로 변환하여 던지면, 호출자는 이 Unchecked Exception을 처리하면서도 예외 정보를 전달하고 로직의 흐름을 끊을 수 있다.
이를 테면, `main` 메서드에서는 `RuntimeException`을 잡아서 처리하면서도, 이 예외에 담긴 정보를 통해 문제의 원인을 파악하고 적절한 조치를 취할 수 있게 된다.



#### 📖 throw vs throws ?

`throw` 는 예외를 명시적으로 발생시킬 때 사용된다. 개발자가 직접 예외를 생성하여 프로그램의 실행을 중단하고자 할 때 사용하는 것이다. 일반적으로 throw new ExceptionType("Exception Message"); 형태로 사용됨.  throw문은 발생되면 즉시 현재 메서드에서 빠져나가게 되며, throw 아래에 있는 코드는 실행되지 않고, 예외가 발생한 시점부터 해당 메서드를 빠져나가게 됨. 

`throws`는 메서드에서 발생할 수 있는 예외를 메서드 선언부에 명시할 때 사용된다. 메서드에서 특정 예외를 처리하지 않고, 메서드를 호출한 곳으로 예외를 전파할 때 사용된다. 여러 개를 선언할 때 쉼표로 구분하게 된다. 

```
public void exampleMethod() throws IOException, CustomException {
    // 메서드 구현
}
```

즉, 둘의 차이를 간단히 말하면, `throw`는 예외를 발생시키는 데 사용되고, `throws`는 메서드에서 발생할 수 있는 예외를 선언하는 데 사용됩니다. 이들은 서로 다른 역할을 수행하며, 함께 사용될 수 있다. 예를 들어, 메서드 내에서 예외를 발생시키고, 이 예외를 메서드 선언부에서 `throws`로 선언하여 메서드를 호출한 곳으로 전파할 수 있다.




#### 📖  예외 처리(Handling)

`CheckedException` 처리 

- `CheckedException`은 컴파일러가 예외 처리를 강제하는 예외로, `try ~ catch` 문이나 `throws` 절을 사용하여 예외를 처리해야한다.
- throws(의존관계) 로 처리한다. 이 말은 더 상위 메서드로 예외를 전파하는 등의 조치를 취하라는 것. 



`UncheckedException(RuntimeException)` 처리 

- Checked E xception이어도 더 구체적인 UnCheckedException으로 발생시킨다.
  - 예를 들어 `IOException` (CheckedException)이 발생하면 이를 좀 더 구체적인 `RuntimeException`으로 변환하여 예외를 발생시킨다.
- throw로 exception을 던지고 ExceptionHandler로 처리한다. 
  - `throw` 문을 사용하여 예외를 발생시키고, 이를 처리하는 `ExceptionHandler`를 통해 예외를 처리한다. ExceptionHandler는 해당 예외에 대한 특별한 처리를 수행하는 코드 블록이며, 주로 `catch` 블록에서 예외를 처리하는데 사용된다.





#### ⚠️e.printStackTrace()

`e.printStackTrace()`에서 기존 예외를 포함하여 스택 트레이스를 출력하는 것은 디버깅 및 예외 추적을 도움으로써 발생한 예외에 대한 정보를 보다 상세하게 확인할 수 있도록 하는 것이다. 스택 트레이스를 출력하는 것은 아래의 이유에 있다.

- 디버깅 용이성
- 문제의 근본 원인 파악
- 전체적인 컨텍스트 파악

```
java.lang.Exception: 예외 메시지
	at 패키지명.클래스명.메서드명(파일명:라인번호)
	at 패키지명.클래스명.다른메서드명(파일명:라인번호)
	...
```

