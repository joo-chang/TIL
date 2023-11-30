# DFS와 BFS 


|시간 제한|메모리 제한|제출|정답|맞힌 사람|정답 비율|
|---|---|---|---|---|---|
|2 초|128 MB|261016|100534|59797|37.308%|

## 문제

그래프를 DFS로 탐색한 결과와 BFS로 탐색한 결과를 출력하는 프로그램을 작성하시오. 단, 방문할 수 있는 정점이 여러 개인 경우에는 정점 번호가 작은 것을 먼저 방문하고, 더 이상 방문할 수 있는 점이 없는 경우 종료한다. 정점 번호는 1번부터 N번까지이다.

## 입력

첫째 줄에 정점의 개수 N(1 ≤ N ≤ 1,000), 간선의 개수 M(1 ≤ M ≤ 10,000), 탐색을 시작할 정점의 번호 V가 주어진다. 다음 M개의 줄에는 간선이 연결하는 두 정점의 번호가 주어진다. 어떤 두 정점 사이에 여러 개의 간선이 있을 수 있다. 입력으로 주어지는 간선은 양방향이다.

## 출력

첫째 줄에 DFS를 수행한 결과를, 그 다음 줄에는 BFS를 수행한 결과를 출력한다. V부터 방문된 점을 순서대로 출력하면 된다.

## 예제 입력 1 복사

4 5 1
1 2
1 3
1 4
2 4
3 4

## 예제 출력 1 복사

1 2 4 3
1 2 3 4

## 예제 입력 2 복사

5 5 3
5 4
5 2
1 2
3 4
3 1

## 예제 출력 2 복사

3 1 2 5 4
3 1 4 2 5

## 예제 입력 3 복사

1000 1 1000
999 1000

## 예제 출력 3 복사

1000 999
1000 999


---
### 문제 풀이


```java
package hello.core;  
  
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

class Main {
    static ArrayList<Integer>[] lists;
    static boolean[] visited;
    static Queue<Integer> queue;
    public static void main(String[] args) throws IOException {
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(bf.readLine());

        int n = Integer.parseInt(st.nextToken());
        int m = Integer.parseInt(st.nextToken());
        int v = Integer.parseInt(st.nextToken());
        lists = new ArrayList[n+1];
        visited = new boolean[n+1];

        for(int i = 0 ; i < n + 1; i++){
            lists[i] = new ArrayList<>();
        }

        for (int i = 0; i < m; i++) {
            st = new StringTokenizer(bf.readLine());
            int a = Integer.parseInt(st.nextToken());
            int b = Integer.parseInt(st.nextToken());
            //양방향 노드 세팅
            lists[a].add(b);
            lists[b].add(a);
        }
        for (int i = 0; i < lists.length; i++) {
            Collections.sort(lists[i]);
        }

        dfs(v);
        System.out.println();
        ////// BFS
        // 방문 배열 초기화
        visited = new boolean[n+1];
        queue = new LinkedList<>();
        bfs(v);
    }

    // 3부터 시작
    // 3 true 3이랑 연결 노드 도는데 1들어가고 2들어가. true로 만들어.3 1 2 5
    static void dfs(int s){
        if(visited[s]) return;
        visited[s] = true;
        System.out.print(s+" ");
        for (int i : lists[s]) {
            if (!visited[i]) {
                dfs(i);
            }
        }
    }

    static void bfs(int s) {
        queue.add(s);
        visited[s] = true;
        System.out.print(s + " ");
        for (int i = 0; i < lists.length; i++) {
            if(!queue.isEmpty()){
                int pop = queue.poll();
                for (int node : lists[pop]) {
                    if (visited[node]) continue;
                    System.out.print(node + " ");
                    queue.add(node);
                    visited[node] = true;
                }
            }else return;
        }
    }
}

```