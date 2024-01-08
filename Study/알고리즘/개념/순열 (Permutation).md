> 순열이란, 임의의 집합을 순서를 부여하여 차례로 나열하는 것을 말한다.

예를 들어 집합 {1, 2, 3} 중 2개의 원소를 선택한 순열을 구하면 {12, 13, 23, 21, 31, 32} 6가지가 나온다.

순열의 경우의 수를 일반화하면, `n개의 수 중 r개를 선택` 하는 순열은 총 `n!/(n-r)!` 개의 경우의수를 가진다.

![[Pasted image 20240108134818.png]]

순열은 재귀를 이용하면 쉽게 구현 가능하다.

1. 대상 집합을 순회하며 숫자를 하나 선택하는 것을 다음과 같이 반복한다.
	1. 만약 숫자가 이미 선택되었으면 그 숫자는 선택할 수 없으므로 스킵
	2. 숫자가 선택되지 않았다면 방문 표시를 하고 재귀 호출한다.
2. 선택된 숫자가 r개가 된다면 재귀를 종료한다.

```java
public class Main{
	static int[] target = new int[]{1, 2, 3};
	static boolean[] visited = new boolean[3];

	public static void main(String[] args){
		permutation(0,"");
	}

	static void permutation(int cnt, String result){
		// 2개를 선택했으므로 결과를 출력하고 재귀를 종료
		if(cnt == 2){
			System.out.println(result);
			return;
		}
		
		for(int i = 0; i < 3; i++){
			if(!visited[i]){
				// 방문 표시
				visited[i] = true;
				// 재귀 호출
				permutation(cnt + 1, result + target[i]);
				// 방문 표시 해제
				visited[i] = false;
			}
		}
	
	}
}
```

