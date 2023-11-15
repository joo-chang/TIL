##### 문제 설명

양의 정수 `n`이 주어집니다. 이 숫자를 `k`진수로 바꿨을 때, 변환된 수 안에 아래 조건에 맞는 소수(Prime number)가 몇 개인지 알아보려 합니다.

- `0P0`처럼 소수 양쪽에 0이 있는 경우
- `P0`처럼 소수 오른쪽에만 0이 있고 왼쪽에는 아무것도 없는 경우
- `0P`처럼 소수 왼쪽에만 0이 있고 오른쪽에는 아무것도 없는 경우
- `P`처럼 소수 양쪽에 아무것도 없는 경우
- 단, `P`는 각 자릿수에 0을 포함하지 않는 소수입니다.
    - 예를 들어, 101은 `P`가 될 수 없습니다.

예를 들어, 437674을 3진수로 바꾸면 `211`0`2`01010`11`입니다. 여기서 찾을 수 있는 조건에 맞는 소수는 왼쪽부터 순서대로 211, 2, 11이 있으며, 총 3개입니다. (211, 2, 11을 `k`진법으로 보았을 때가 아닌, 10진법으로 보았을 때 소수여야 한다는 점에 주의합니다.) 211은 `P0` 형태에서 찾을 수 있으며, 2는 `0P0`에서, 11은 `0P`에서 찾을 수 있습니다.

정수 `n`과 `k`가 매개변수로 주어집니다. `n`을 `k`진수로 바꿨을 때, 변환된 수 안에서 찾을 수 있는 **위 조건에 맞는 소수**의 개수를 return 하도록 solution 함수를 완성해 주세요.

---

##### 제한사항

- 1 ≤ `n` ≤ 1,000,000
- 3 ≤ `k` ≤ 10

---

##### 입출력 예

|n|k|result|
|---|---|---|
|437674|3|3|
|110011|10|2|

---
### 문제 풀이

```java
import java.util.*;
class Solution {
    public int solution(int n, int k) {
        int answer = 0;
        // 진법 변환해서 배열이 비어있거나 1보다 작거나 같으면 패스
        // 소수인지 판별해서 answer 증가
        String s = Integer.toString(n, k);
        String[] split = s.split("0");

        for(String sp : split){
            if(sp.isEmpty() || Long.parseLong(sp) <= 1) continue;
            if(decimal(Long.parseLong(sp))) answer++;
        }
        
        return answer;
    }
    
    public boolean decimal(long num){
        for(int i = 2; i<= Math.sqrt(num); i++){
            if(num%i == 0) return false;
        }
        return true;
    }
}
```