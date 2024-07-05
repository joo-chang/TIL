### 깊이 우선 탐색 (Depth-First Search)

> 최대한 깊이 내려간 뒤, 더이상 깊이 갈 곳이 없을 경우 옆으로 이동

#### 깊이 우선 탐색 개념

루트 노드(혹은 다른 임의의 노드)에서 시작해서 다음 분기로(branch)로 넘어가기 전에 해당 분기를 완벽하게 탐색하는 방식을 말한다.

미로 찾기 할 때, 최대한 한 방향으로 갈 수 있을 때까지 쭉 가다가 더 이상 갈 수 없게 되면 다시 가장 가까운 갈림길로 돌아와서 다시 탐색을 진행하는 것을 깊이 우선 탐색 방식이라고 한다.

1. 모든 노드를 방문하고자 하는 경우 이 방법을 선택한다.
2. 깊이 우선 탐색이 너비 우선 탐색보다 좀 더 간단하다.
3. 검색 속도 자체는 너비 우선 탐색에 비해 느리다.

### 너비 우선 탐색 (Breadth-First Search)

> 최대한 넓게 이동한 다음, 더 이상 갈 수 없을 때 아래로 이동

#### 너비 우선 탐색의 개념

루트 노드에서 시작해서 인접한 노드를 먼저 탐색하는 방법으로, 시작 정점으로부터 가까운 정점을 먼저 방문하고 멀리 떨어져 있는 정점을 나중에 방문하는 순회 방법이다.

주로 두 노드 사이의 최단 경로를 찾고 싶을 때 이 방법을 선택한다.
ex) 지구 상 존재하는 모든 친구 관계를 그래프로 표현한 후 a와 b 사이에 존재하는 경로를 찾는 경우

- 깊이 우선 탐색 - 모든 친구 관계를 다 살펴봐야 할 수도 있다.
- 너비 우선 탐색 - a와 가까운 관계부터 탐색한다.

### 너비우선탐색 (BFS)

- 정점(노드)과 같은 레벨에 있는 노드(=형제 노드)를 먼저 탐색하는 방식

![[Pasted image 20230127233447.png]]



### 깊이우선탐색 (DFS)

- 정점(노드)의 자식 노드를 먼저 탐색하는 방식

![[Pasted image 20230127233831.png]]
 
<br>

### 최단 경로 알고리즘 (Shortest Path Algorithm)

- 그래프에서 두 노드를 잇는 가장 짧은 경로를 찾는 알고리즘이다.
- 간선(edge)의 가중치 합이 최소인 경로를 찾는 것이 목적이다.
- 최단 경로 알고리즘에서는 3가지 문제 유형이 있다.
	- 단일 출발(single-source) : 특정 노드와 그 외 노드간의 최단경로
	- 단일 출발 및 단일도착(single-source & single-destination) : 특정 노드 2개의 최단 경로
	- 전체 쌍(all-pair) : 모든 노드간의 연결 조합에 대한 최단 경로



### 예제

![[Pasted image 20240705100135.png]]


```java
import java.util.ArrayList;  
import java.util.LinkedList;  
import java.util.Queue;  
  
class Main {  

	public static ArrayList<ArrayList<Integer>> graph;
	public static boolean[] visited; 
    public static void main(String[] args){  
        graph = new ArrayList<>();  
        
        // 리스트 초기화  
        for(int i = 0; i < 3; i++){  
            graph.add(new ArrayList<Integer>());  
        }  
		visited = new boolean[graph.size() + 1]; 
		
        // 노드1에 연결된 노드 정보 저장  
        graph.get(1).add(2);  
        graph.get(1).add(3);  
        graph.get(1).add(8);  
  
        // 노드2에 연결된 노드 정보 저장  
        graph.get(2).add(1);  
        graph.get(2).add(7);  
  
        // 노드3에 연결된 노드 정보 저장  
        graph.get(3).add(1);  
        graph.get(3).add(4);  
        graph.get(3).add(5);  
  
        // 노드4에 연결된 노드 정보 저장  
        graph.get(4).add(3);  
        graph.get(4).add(5);  
  
        // 노드5에 연결된 노드 정보 저장  
        graph.get(5).add(3);  
        graph.get(5).add(4);  
  
        // 노드6에 연결된 노드 정보 저장  
        graph.get(6).add(7);  
  
        // 노드7에 연결된 노드 정보 저장  
        graph.get(7).add(2);  
        graph.get(7).add(6);  
        graph.get(7).add(8);  
  
        // 노드8에 연결된 노드 정보 저장  
        graph.get(8).add(1);  
        graph.get(8).add(7);  
  
        bfs(1);  
  
  
    }  
  
    private static void bfs(int start) {  
        Queue<Integer> queue = new LinkedList<>();  
         
        queue.add(start);  
        visited[start] = true;  
  
        while(!queue.isEmpty()){  
            int target = queue.poll();  
  
            for(int n : graph.get(target)){  
                if(!visited[n]){  
                    queue.add(n);  
                    visited[n] = true;  
                }  
            }  
        }  
    }  

	public static void dfs(int point){
		visited[point] = true;
		
		for(int n : graph.get(point)){
			if(!visited[n]){
				dfs(n);
			}
		}
	}
  
}
```

<br>