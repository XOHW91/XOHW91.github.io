---
layout: single
title:  "[Java] BufferedReader vs Scanner"
---

백준을 풀다보면 사용자에게 값을 입력받아서 처리하는 경우가 대부분이다.

15552번 문제를 만나기 전까지는 Scanner 객체를 이용해 문제를 풀었는데

위 문제에서 BufferReader와 Scanner의 차이점을 알게 돼 기록한다.



> **BufferedReader** vs **Scanner**
>
> 우선 BufferedReader와 Scanner는 Java에서 문자 입력을 받기 위한 클래스라는 점에서 같다.
>
> Scanner의 경우 다양한 메서드(`nextInt(), nextDouble`)등을 이용하여 기본 자료형의 값을 얻을 수 있지만,  문자열을 읽는 BufferedReader의 readLine()보다 성능적으로 속도가 느린 것이 큰 차이점이다. 때문에 대용량 데이터를 처리하는 경우에는 Scanner보다 BufferedReader를 사용 하는 것이 적합하다.



아래 예시로 BufferedReader와 BufferedWriter를 공부해보자.

```
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.*;
import java.util.StringTokenizer;

public class backjun {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
        int T = Integer.parseInt(br.readLine());

        StringTokenizer st;
        for (int i = 0; i < T; i++) {
            st = new StringTokenizer(br.readLine(), " ");
            bw.write((Integer.parseInt(st.nextToken()) + Integer.parseInt(st.nextToken()))+ "\n");
        }
        bw.flush();
    }
}
```

### BufferedReader

- 데이터를 문자로 읽어들이며, 주로 파일이나 다른 리더와 함께 사용된다. 
- 버퍼링을 사용하여 입출력 성능을 향상시키기 때문에 대량의 데이터를 효율적으로 처리할 수 있다.
- 예외처리가 필요하며 IOException을 처리해야 한다. 
- 기본적으로 문자열을 읽기 때문에 문자열을 파싱하려면 추가적인 작업이 필요하다.



### InputStreamReader

- Java에서 바이트 단위로 데이터를 읽어오는 입력스트림인 InputStream을 문자 단위 데이터로 읽어오는 입력스트림인 Reader로 변환하는 역할을 한다. 



### BufferedWriter

- 문자 출력 스트림에 버퍼링 기능을 제공하는 클래스이다. 즉, 데이터를 일정량만큼 모아서 한 번에 출력하므로 **출력 성능을 향상시킬 수 있다.** 

- `write()` - 문자열을 출력 스트림에 작성하는 메서드. (즉, 버퍼에 저장한다고 보면 된다.)

- `newLine()` - 새로운 줄로 이동하는 메서드

- `flush()`- 버퍼에 저장한 문자열을 한꺼번에 출력을 해주는 메서드 

  >flush()를 하게 되면 버퍼에 저장한 문자열을 한꺼번에 출력을 해주기 때문에 
  >
  >write()를 이용해서 버퍼에 저장할 때부터 이스케이프 개행 문자인 "\n"을 사용하여 줄바꿈도 포함하여 저장해야 함. 



### OutputStreamWriter

- 바이트 출력 스트림인 OutputStream을 문자 출력 스트림인 Writere로 변환해주는 메서드이다.

- System.out은 콘솔에 데이터를 출력하는 데 사용된다. 

   

### StringTokenizer

- StringTokenizer 클래스는 문자열을 구분자(delimiter)를 기준으로 토큰(token)으로 나누기 위한 클래스.
- 위 예시에서 br.readLine()은 BufferedReader를 사용하여 한 줄을 읽어오는데 읽어온 문자열을 공백(" ")을 기준으로 나누어서 StringTokenizer 객체 st에 저장한다.
- 이후 코드에서는 st.nextToken() 메서드를 사용하여 토큰을 하나씩 읽어올 수 있는데 이때 순서대로 읽어옴.



### br.read() vs br.readLine()

- br.read() 

  한번에 하나의 문자만 읽어오며, 반환 값은 문자의 ASCII 코드 값이다. 보통은 int로 받아서 문자로 변환하여 사용함.

  > int charCode = br.read();
  >
  > char character = (char) charCode; 



- br.readLine() 

  한 줄씩 문자열을 읽어 오며, 반환 값은 문자열. 데이터가 없으면 null을 반환하게 된다. 
