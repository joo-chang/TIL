
Dijkstra 알고리즘은 하나의 정점에서 출발했을 때 다른 모든 정점으로의 **최단 경로**를 구하는 알고리즘이다.

- DP를 활용한 최단 경로 탐색 알고리즘으로 흔히 인공위성 GPS 소프트웨어에서 가장 많이 사용된다.

![[Pasted image 20240529110529.png]]

위 그림을 아래와 같이 2차원 배열로 만들 수 있다.
  
|       | **1** | **2** | **3** | **4** | **5** | **6** |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **1** | 0     | 2     | 5     | 1     | inf   | inf   |
| **2** | 2     | 0     | 3     | 2     | inf   | inf   |
| **3** | 5     | 3     | 0     | 3     | 1     | 5     |
| **4** | 1     | 2     | 3     | 0     | 1     | inf   |
| **5** | inf   | inf   | 5     | 1     | 0     | 2     |
| **6** | inf   | inf   | 5     | inf   | 2     | 0     |

1번 노드로부터 연결된 노드 중 가장 비용이 적은 순서대로 최단 경로를 찾아간다.

1번 노드를 기준으로 최단 경로를 구하면 직접 연결된 노드는 2, 3, 4로 가장 적은 비용이 드는 4번 노드가 최단 경로이다.

|       | **1** | **2** | **3** | **4** | **5** | **6** |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **1** | 0     | 2     | 5     | 1     | inf   | inf   |

5로 가는 최소 비용은 inf 이지만 4를 거쳐 5로 가는 비용은 2이므로 2로 갱신해준다.
또한, 4를 거쳐 3으로 가는 경우 비용이 4로 기존보다 작으므로 4로 갱신해준다.

|       | **1** | **2** | **3** | **4** | **5** | **6** |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **1** | 0     | 2     | **4** | 1     | **2** | inf   |

4번 다음으로 비용이 적은 노드는 2번 노드이지만 최단 경로가 갱신되는 경우가 없기 때문에 배열은 유지된다.
다음은 5번 노드(값 : 2)를 탐색한다.
5번에서 3, 6번을 갈 수 있는데, 3번 비용은 3이고, 6번 비용은 4이므로 배열을 갱신한다.

|       | **1** | **2** | **3** | **4** | **5** | **6** |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **1** | 0     | 2     | **3** | 1     | 2     | **4** |

이런 식으로 3, 6 노드 모두 확인하면 1번 노드로부터 최단 경로가 완성된다.

|       | **1** | **2** | **3** | **4** | **5** | **6** |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| **1** | 0     | 2     | 3     | 1     | 2     | 4     |

```java
import java.util.*;

public class Dijkstra {
    // 다익스트라 알고리즘 구현 메서드
    public static void dijkstra(int start, List<int[]>[] graph) {
        int n = graph.length; // 노드의 수
        int[] dist = new int[n]; // 시작 노드로부터 각 노드까지의 최단 거리를 저장하는 배열
        boolean[] visited = new boolean[n]; // 각 노드의 방문 여부를 저장하는 배열

        Arrays.fill(dist, Integer.MAX_VALUE); // 초기 거리 값을 무한대로 설정
        dist[start] = 0; // 시작 노드의 거리는 0으로 설정

        // 우선순위 큐를 사용하여 현재까지의 최단 거리를 가진 노드를 선택
        // 배열의 두 번째 요소(거리)를 기준으로 최소 힙을 형성
		PriorityQueue<int[]> queue = new PriorityQueue<>((o1, o2) -> {
		    return Integer.compare(o1[1], o2[1]);
		});

        pq.offer(new int[] {start, 0}); // 시작 노드를 큐에 추가

        while (!pq.isEmpty()) {
            int[] current = pq.poll(); // 큐에서 가장 짧은 거리를 가진 노드를 선택
            int currentVertex = current[0]; // 현재 노드

            if (visited[currentVertex]) continue; // 이미 방문한 노드는 건너뜀
            visited[currentVertex] = true; // 현재 노드를 방문한 것으로 표시

            // 현재 노드의 모든 인접 노드를 검사
            for (int[] neighbor : graph[currentVertex]) {
                int nextVertex = neighbor[0]; // 인접 노드
                int weight = neighbor[1]; // 인접 노드로 가는 가중치

                // 아직 방문하지 않았고, 현재 노드를 거쳐가는 경로가 더 짧다면
                if (!visited[nextVertex] && dist[currentVertex] + weight < dist[nextVertex]) {
                    dist[nextVertex] = dist[currentVertex] + weight; // 최단 거리 갱신
                    pq.offer(new int[] {nextVertex, dist[nextVertex]}); // 갱신된 최단 거리를 큐에 추가
                }
            }
        }

        // 최단 거리 출력
        for (int i = 0; i < n; i++) {
            if (dist[i] == Integer.MAX_VALUE) {
                System.out.println("Node " + i + " is unreachable"); // 도달 불가능한 경우
            } else {
                System.out.println("Distance from start to node " + i + " is " + dist[i]); // 최단 거리 출력
            }
        }
    }

    // 메인 메서드
    public static void main(String[] args) {
        int n = 5; // 노드의 수
        List<int[]>[] graph = new ArrayList[n]; // 그래프를 인접 리스트로 표현

        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>(); // 각 노드에 대한 리스트 초기화
        }

        // 그래프 초기화 (예제)
        graph[0].add(new int[] {1, 10});
        graph[0].add(new int[] {4, 5});
        graph[1].add(new int[] {2, 1});
        graph[1].add(new int[] {4, 2});
        graph[2].add(new int[] {3, 4});
        graph[3].add(new int[] {0, 7});
        graph[3].add(new int[] {2, 6});
        graph[4].add(new int[] {1, 3});
        graph[4].add(new int[] {2, 9});
        graph[4].add(new int[] {3, 2});

        dijkstra(0, graph); // 시작 노드 0에서 다익스트라 알고리즘 실행
    }
}

```