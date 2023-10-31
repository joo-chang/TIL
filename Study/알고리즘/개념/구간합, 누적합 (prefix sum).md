
누적합(구간합) 은 말 그대로 구간의 누적합을 구하는 문제이다.

배열에 값을 저장하고 지정된 인덱스부터 하나씩 더해가는 방식은 최악의 경우 O(n^2)의 시간 복잡도를 갖기 때문에 입력의 범위가 클 때 사용할 수 없다. 하지만 누적합을 사용하면 O(n)으로 해결할 수 있다.

| 인덱스   | 1   | 2   | 3   | 4   | 5   | 6   |
| ------ | --- | --- | --- | --- | --- | --- |
| 배열   | 5   | 6   | 3   | 9   | 1   | 2   |
| 누적합 | 5   | 11  | 14  | 23  | 24  | 26  |

예를 들어 3번 부터 5번까지의 누적합을 구하려면

5번 구간합에서 2번 구간합을 빼주면 된다.

```java
import java.util.Arrays;

public class Main {
	public static void main(String[] args){
		int[] array = {5, 6, 3, 9, 1, 2};
		int[] prefixSum = new int[array.length];
		
		for(int i = 0; i < array.length; i++){
			for(int j = 0; j <= i; j++){
				prefixSum[i] += array[i];
			}
		}
	}
}
```

위와 같이 구현할 경우 누적합을 구할 때, 처음 인덱스부터 다시 더하는 방식으로 O(n^2) 시간 만큼 걸린다.

```java
import java.util.Arrays;

public class Main {
	public static void main(String[] args){
		int[] array = {5, 6, 3, 9, 1, 2};
		int[] prefixSum = new int[array.length];
		prefixSum[0] = array[0];
		for(int i = 1; i < array.length; i++){
			prefixSum[i] = prefixSum[i - 1] + array[i];
		}
	}
}
```

위 코드가 O(n) 시간이 걸리는 누적합 알고리즘이다.

