  

|시간 제한|메모리 제한|제출|정답|맞힌 사람|정답 비율|
|---|---|---|---|---|---|
|10 초|128 MB|105968|50750|32934|46.486%|

## 문제

N-Queen 문제는 크기가 N × N인 체스판 위에 퀸 N개를 서로 공격할 수 없게 놓는 문제이다.

N이 주어졌을 때, 퀸을 놓는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N이 주어진다. (1 ≤ N < 15)

## 출력

첫째 줄에 퀸 N개를 서로 공격할 수 없게 놓는 경우의 수를 출력한다.

## 예제 입력 1 복사

8

## 예제 출력 1 복사

92

<br>

## 문제 풀이

![[Pasted image 20231230095836.png]]

**N x N 판에서 N개의 퀸**을 서로 공격하지 않게 놓는 경우의 수


```java
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStreamReader;  
  
public class Main {  
    static int n;  
    static int count;  
    public static void main(String[] args) throws IOException {  
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));  
        n = Integer.parseInt(bf.readLine());  
        //하나의 열에 하나의 퀸만 들어갈 수 있기 때문에 1차원 배열 선언  
        int[] arr = new int[n + 1];  
  
        dfs(arr, 0);  
  
        System.out.println(count);  
  
    }  
    private static void dfs(int[] arr, int depth) {  
	    // depth가 곧 퀸의 개수
        if (depth == n) {  
            count++;  
        }  
  
        for (int i = 0; i < n; i++) {  
	        // arr[0] 은 첫번째 열
	        // arr[0] = 0, arr[0] = 1 이런 식으로 퀸의 위치를 설정
	        // 하나의 열에는 하나의 퀸만 세울 수 있음
            arr[depth] = i;  
            
            // 여기서 백트래킹 기법 사용
            // 놓을 수 있는 경우에만 재귀호출 dfs 실행
            if (Possibility(arr, depth)) {  
                dfs(arr, depth + 1);  
            }  
        }  
    }  
    public static boolean Possibility(int[] arr, int col) {  
  
        for (int i = 0; i < col; i++) {  
            // 해당 열의 행과 i열의 행이 일치할경우 (같은 행에 존재할 경우)  
            if (arr[col] == arr[i]) {  
                return false;  
            }  
  
		    // abs = 절대값 반환
		    // 열의 차와 행의 차가 같을 경우 대각선에 놓여있다.
		    // [3, 2] 의 대각선 [2, 3](1 차이), [2, 1](1 차이), [1, 0](2 차이)
		    // [3, 1] 의 대각선 [2, 2](1 차이), [1, 3](2 차이)...
            else if (Math.abs(col - i) == Math.abs(arr[col] - arr[i])) {  
               return false;  
            }  
        }  
  
        return true;  
    }  
}
```