---
layout: single
title:  "[Java] EOF(End of File)"
typora-root-url: ../
---





# ✏️ EOF(End of File) 이란?

우선, EOF란 입력을 받는 리소스로부터 더 이상 읽어들일 수 있는 데이터가 없는 상태를 의미한다.
반복문을 작성하다보면 일반적으로 for문은 반복 횟수가 명확하게 정해진 경우, while은 반복 횟수가 명확하지 않으나 조건이 만족하는 동안 반복되는 경우에 쓰인다고 배운다. 

끝맺음 구간이 명확하지 않을 때는 어떻게 해야할까? 
주로 EOF 처리를 해주어서 무한루프에 빠지지 않도록 해야 하는데 EOF 처리는 파일에서 입력을 읽을 때 주로 사용되며, 파일의 끝을 확인하여 반복문을 종료하는 역할을 한다. 

> 예를 들어, 사용자로부터 입력을 받아야 하는 상황에서 사용자가 특정한 입력을 하지 않으면 반복문을 종료하는 등의 경우에도 EOF 처리를 해줄 수 있다. 



이 문제는 백준 [10951]번 문제를 풀면서 마주하게 됐으며, EOF는 `Scanner`와 `BufferedReader` 모두 처리 가능하니 모두 숙지할 것.



#### Scanner를 사용할 때의 EOF 

```
import java.util.Scanner;

public class Main() {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		// Scanner의 경우에는 hasNext(), hasNextLine() 메서드를 사용하면 됨.
		while(sc.hasNextLine()) { // 만약 입력받은 값이 있으면 true이기 때문에 계속해서 반복문 돔
			String line = sc.nextLine(); 
			
			if(line.isEmpty()) { // 빈 문자열이라면?
				break;
			}
			
			String[] i = line.split(" ");
			int A = Integer.parseInt(i[0]);
			int B = Integer.parseInt(i[1]);
			
			System.out.println(A+B);
		}
	}
}
```

⚠️ 주의할 점 

- hasNext() : 다음 토큰(공백으로 구분된 문자열)이 있는지를 확인한다. 다음 토큰이 있으면 true, 없으면 false를 반환 

  ==> hasNext()를 통해 확인된 후에 'next()'또는 'nextXXX()'메서드로 실제 값을 읽을 수 있다.

- hasNextLine() : 다음 라인이 있는 지를 확인한다. 다음 라인이 있으면 true, 없으면 false를 반환

  ==> hasNextLine()을 통해 확인된 후에 'nextLine()' 메서드로 실제 라인을 읽을 수 있다 .





#### BufferdReader를 사용할 때의 EOF

```
import java.io.*;
import java.util.Scanner;

public class Main() {
	public static void main(String[] args) {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		BufferedWriter bw = new BufferedWriter(new OutputStreamReader(System.out));
		
		while(true) {
			String line = br.readLine();
			if(line==null || line.trim().isEmpty()) { // 사용자가 아무것도 입력하지 않았거나, 공백만 입력한 경우
													  // trim으로 앞 뒤 공백 잘랐는데 공백인 경우
				break;
			}
			
			// 입력한 값이 있다면 공백을 기준으로 토크나이저로 문자열 자름.
			StringTokenizer st = new StringTokenizer(line, " ");
			if(st.hasMoreTokens()) {
				int A = Integer.parseInt(st.nextToken());
				int B = Integer.parseInt(st.nextToken());
				bw.write(String.valueOf(A+B), "\n");
			}
		}
		bw.flush();
		br.close();
		bw.close(); // 메무리 누수를 방지하고 사용 중인 리소스를 제대로 해제하기 위해 close 해 준다. 
	}
}
```

