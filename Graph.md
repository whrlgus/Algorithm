# Graph

## 1. Graph Representation

graph는 object들의 집합으로 object 쌍의 관계를 표현할 수 있는 자료구조이다. 이러한 graph는 두 가지의 기본 요소인 **node** (or vertex)와 **edge** 로 시각화할 수 있다.

- **node** 는 edge를 이용해 다른 node와의 관계가 표현되는 개체이다.
- **edge**는 graph에서 다양한 node들 간의 관계를 표현하는데 사용된다.



### Types of nodes

node는 **root node** 와 **leaf node** 로 나뉜다.

- **root node** 는 graph에서 하나만 존재하며, 다른 모든 node들의 조상(ancestor)에 해당한다. 일반적으로 graph 순회는 root node 부터 시작한다.
- **leaf node** 는 자식 node가 없고 부모 node만 가지며, outgoing edge가 없고 incoming edge만 존재하는 node를 가리킨다.



### Types of graphs

graph의 유형은 크게 4가지로 나뉜다.

- **Undirected** graph는 모든 edge가 특정 방향을 가리키지 않아 양방향인 graph를 말한다.

  <img src = "https://he-s3.s3.amazonaws.com/media/uploads/5069914.jpg" width = "300">

- **Directed** graph는 edge가 한쪽 방향을 가리키는 단방향 graph이다.

  <img src = "https://he-s3.s3.amazonaws.com/media/uploads/59ec874.jpg" width = "300">

- **Weighted** graph는 각 edge에 weight나 cost를 부여하여 표현되는 graph를 말한다.

  <img src = "https://he-s3.s3.amazonaws.com/media/uploads/6fd9992.jpg" width = "300">

- **Cyclic** graph는 시작 vertex가 동시에 end vertex가 되는 그래프로 한 정점에서 다른 정점까지 가는 path에 cycle이 존재한다. 반면, **Acyclic** graph는 cycle이 없는 graph를 말한다. **tree** 는 root node를 제외한 모든 node들이 단 하나의 부모 node만 가지는, edge의 개수가 vetex의 개수보다 1개 적은 그래프로, acyclic graph에 해당한다.



graph를 표현하기 위한 대표적인 방법은 **인접 행렬** 표현과 **인접 리스트** 표현 방식이 있다.

```c++
bool A[10][10]; // adjacency matrix
vector <int> adj[10]; // adjacency list
```



## 2. Minimum Spanning Tree

undirected and connected graph G의 **spanning tree** 는 G에 걸쳐있는, G의 모든 vertex 들을 포함하는 tree이다. spanning tree의 **cost** 는 tree에 있는 모든 edge에서 weight을 합한 값인데, **minimum spanning tree** 는 이 값이 최소인 spanning tree 이다. 하나의 graph에서 여러개의 minimum spanning tree가 있을 수 있다. 이를 구하는 대표적인 알고리즘은 **kruskal** 과 **prim** 방식이 있다.

### Kruskal's Algorithm

greedy 접근으로 매 순서마다 가장 작은 weight의 edge를 spanning tree에 포함 시켜나간다. 

algorithm steps:

- graph의 edge를 **weight을 기준으로 정렬**한다.
- weight가 작은 edge부터 큰 edge까지 순서대로 추가해 나간다.
- 단, cycle을 형성하지 않는 edge만 추가한다. cycle 형성 여부는 **disjoint sets** (교차점이 빈 set인 공통 요소를 포함하지 않는 sets) 을 이용한다. (Uninon-find 코드 사용)

시간 복잡도는 정렬에 **O(ElogE)** , disjoint-set 동작에 **O(ElogV)** 이다.

<img src = "https://he-s3.s3.amazonaws.com/media/uploads/6322896.jpg" width = "600">

 

### Prim's Algorithm

Prim 방식에서 역시 greedy 접근으로 MST를 찾지만, spanning tree에 edge를 추가하지 않고 **vertex** 를 추가하는 방식으로 진행한다. 

- 두개의 disjoint sets로 vertex들을 관리한다. 하나는 growing spanning tree이고 다른 하나는 추가 되지 않은 정점들에 해당한다.
- growing spanning tree와 인접한 추가되지 않은 정점 중에 가장 작은 weight로 연결된 정점을 추가한다. **priority queue로 후보 정점들을 관리**한다.
- 역시 **cycle을 형성하는지 확인**한다. (정점 방문 여부로 체크)

우선 순위큐를 이용하여 정정을 추가하고 삭제하는 데 O(ElogE), cycle 확인에 O(VlogV)의 시간복잡도를 갖는다.

<img src = "https://he-s3.s3.amazonaws.com/media/uploads/16597fe.jpg" width = "600">



## 3. Shortest Path Algorithm

최단 경로 문제는 2개의 정점간의 최소 weight의 합으로 갈 수 있는 경로를 찾는 것이다. 

### Bellman Ford's Algorithm

**Bellman Ford's algorithm** 은 source vertex에서 다른 모든 vertex로의 최단 경로를 찾을 때 사용한다. 이 알고리즘은 다음과 같은 개념에 의존하고 있다: **최단 경로는 cycle을 가질 수 없기때문에 최대 n-1 개의 edge를 가질 수 있다.** 

Algorithm steps:

- outer loop는 0번 정점부터 n-1번 정점까지 순회하는 것이다.
- Inner loop에서는 모든 edge를 순회하며 `next node distance > current node distance + edge weight` 를 확인하며 `next node distance` 를 갱신해준다.

이 알고리즘은 relaxation principle에 의존하여 모든 정점들이 점진적으로 더 정확한 값을 갖다가 마침내 최적의 solution에 도달한다. 처음에 source 정점은 0, 나머지 정점은 INF로 초기화 하여 갱신해 나간다. 만약, negative weight cycle이 존재하는지 확인하려면 inner loop를 한번더 수행해서 값의 갱신이 있는지 확인하면 된다. 시간 복잡도는 **O(V * E)** 이며 **E = V^2 라면, O(V^3)** 일 것이다.

```c++
int edges[E][3]; // u, v, w 순으로 간선 정보 저장
int dist[V]; // dist[i]는 source 정점으로부터 i 정점까지의 최단 거리
```



### Dijkstra's Algorithm

Dijkstra algorithm은 다른 변형으로 여러 용례가 있지만, 보통 bellman ford algorithm과 같이 source 정점에서 다른 모든 정점으로의 거리를 구할 때 사용된다. 

algorithm steps:

- source 정점의 거리를 0으로 나머지는 INF로 초기화한다.
- Min heap을 사용해 source 정점을 삽입한다.
- heap에서 최소 거리를 갖는 정점을 빼서 인접 정점까지의 거리를 갱신하고 갱신된 정점을 heap에 삽입한다.
- 방문 체크를 하면서 진행한다.

V-1개의 경로를 찾으면 되기 때문에 outer loop에서 **V-1**번 수행하고 매 loop마다 **V**번의 탐색으로 최소 거리를 갖는 정점을 찾고 다른 정점들을 갱신하므로, 시간복잡도는  **O(V^2)** 이 된다. 하지만 min-heap을 사용하면 **O(ElogE)** 로 향상된다.



### Floyd-Warshall's Algorithm

Floyd-warshall 알고리즘은 그래프의 모든 정점 간의 최단 경로를 구할 때 사용한다. 간선의 weight가 음수인 경우에도 사용할 수 있다. O(V^3)의 시간복잡도로 구해진다. 



## 4. Articulation Points and Bridges

### Articulation Point

그래프에서 한 정점이 있고, 만약 이 정점과 연결된 간선을 제거할 때, connected component의 개수가 증가한다면 이 정점을 **articulation point** 라고 부른다. articulation은 접합, 관절과 같은 의미를 가지고 있다. 두개 이상의 component를 잇는 역할을 하기 때문에 저런 이름이 붙은 것 같다. 반면에, 상반된 의미의 cut vertex 라고도 하며 한글 표기로는 단절점이다. 단순하게 정점을 하나씩 제거해가면서 dfs 로 절단 여부를 확인하는 **O(V * (V+E))** 의 시간복잡도가 나오는 알고리즘이 있다. 

개선된 알고리즘은 **O(V+E)** 의 시간복잡도를 가지며 **단 한번의 depth first search** 로 모든 단절점들을 찾아낼 수 있다. graph를 tree라고 여기고, 아무 정점이나 한 개를 root vertex로 정한뒤 root vertex부터 dfs를 수행한다. 이때, **tree edge **와 **back edge** 라는 개념이 사용되는데, 정점 v의 간선을 확인하는 과정에서 자식 정점(실제는 인접 정점)을 이전에 방문 했다면 back edge, 처음 방문한다면 tree edge라고 지칭한다. 매번 정점을 방문할 때마다 최초 방문 시점(tin)을 기록하고, 간선을 탐색하면서 back edge와 tree edge 중의 가장 먼저 방문한 시점(low)을 함께 기록한다. 동시에 tree edge의 low 값과 현재 정점 v의 tin값을 비교하여 단절점 판단 조건을 체크한다.

참고로 단절점을 포함하지 않은 graph를 가리켜 biconnected(이중연결) graph 라고 한다.

### Bridge

단절점과 비슷한 개념으로, 한 edge가 있고, 해당 edge를 제거할 때 connected component의 개수가 증가한다면 이를 Bridge, 단절선이라고 한다. 단절점을 구하는 알고리즘과 비슷하나, root node 여부를 고려할 필요가 없어서 더 간단하다.



## 5. Strongly Connected Components

directed graph에서 모든 정점 쌍간의 경로가 존재할때, 강하게 연결되었다(strongly connected)고 한다. directed graph는 여러개의 SCC로 분리할 수 있으며, 아래 그래프에서 SCC는 두개이다. 그래프에서 SCC를 찾는 알고리즘은 **kosaraju** 방식과 **tarjan** 방식 두 가지가 있다.

<img src="https://he-s3.s3.amazonaws.com/media/uploads/cdc3361.png" width="400">

### Kosaraju's algorithm

dfs를 두번 수행하여 **O(V+E)** 의 시간복잡도로 모든 scc를 찾는 알고리즘이다. 첫번째 dfs 수행을 통해 **방문 완료 시간** 순으로 정렬된 정점들의 리스트를 구한다. 그리고 이 리스트의 정점들을 역순으로 하고, 각 정점을 root node로 하여 dfs를 수행하면 scc를 구할 수 있게된다. 단, scc를 구하기 위한 dfs는 원래의 그래프의 방향과 반대로 수행해야 한다.

<img src="https://media.geeksforgeeks.org/wp-content/cdn-uploads/kosaraju.jpg" width="400">

이 알고리즘의 핵심 개념은 바로 sink 이다. 방향 그래프에서 sink vertex란 out-degree가 0인 것을 말한다. cycle을 형성하는 정점들의 집합을 sink로 본다면 sink는 여러 scc중 하나가 될 것이다. original graph에서 sink를 없앤다면 그 부모가 다음 sink가 될 것이고, 이러한 방식으로 root까지 도달할 수가 있어 모든 scc를 구할 수 있게 된다.

<img src="https://www.geeksforgeeks.org/wp-content/uploads/SCCGraph2.png" width="400">

dfs로 호출하는 재귀함수의 종료 직전에 정점을 stack에 삽입하고, stack의 역순으로 orginal graph의 방향의 반대로 한 dfs를 수행한다면 위의 이론을 실현할 수 있는 것이다. 

Algorithm steps:

- dfs로 방향 그래프의 각 정점을 순회하면서 재귀함수 종료 직전에 정점을 스택에 담는다.
- 스택의 역순으로 각 정점을 root node로 하여, 역방향의 인접 리스트를 사용하여 dfs를 실행하며, 방문한 정점들을 하나의 scc로 그룹화한다.



### Tarjan's algorithm

tarjan 방식은 kosaraju와 다르게 단 한번의 dfs로 모든 scc를 구할 수가 있다. 그래프의 단절점을 구할 때 사용했던 개념을 이용한다. `tin[u]` 은 정점을 처음 방문한 시간이고, `low[u]` 는 정점 u를 root로 하여 방문 가능한 최소 시간을 나타낸다. 즉, `low[u]` 가 `tin[u]` 보다 작다는 말은 정점 u가 back edge를 갖는다는 것이다. tarjan 방식도 결국은 kosaraju 방식과 비슷하게 graph의 sink 부터 scc list에 기록한다. 노드를 방문한 순서대로 stack에 정점을 삽입하고, subtree의 head를 찾으면, 다시말해, 자식 노드 방문을 마친 후 `tin[u] == low[u]` 라면, stack의 top 부터 head 까지의 정점들이 하나의 scc인 것이다.



<img src="https://media.geeksforgeeks.org/wp-content/cdn-uploads/tarjan.jpg" width="400">

Algorithm details:

- 정점 u의 자식 정점 v가 방문 전이라면, 해당 간선은 `tree edge` 이며 `low[u]` 는 v의 dfs를 마친 후 `low[v]` 와 비교하여 최소값을 갱신한다.
- 만약 방문했다면, 해당 간선은 `back edge` 에 해당하며, dfs를 진행하지 않고 `low[u]` 는 `tin[v]` 와 비교하여 최소값을 갱신한다.
- 하지만 방문한 경우에 `back edge` 인지 `cross edge` 인지 구분해야 한다.  `cross edge` 로 연결된 정점은 이미 scc 로 검출했기 때문에 더이상 고려하면 안된다. 방문 정점 stack에 포함된 정점이라면 `back edge` 로 연결된 것이고, 그렇지 않다면 `cross edge` 로 연결된 것이다.



## Topological Sort

그래프에서 정점들의 위상 정렬은 directed acyclic graph인 경우에만 가능하다. 정점의 in-degree가 0인 node부터 탐색하면서 인접 정정의 in-degree를 하나씩 없애고, 결과로 in-degree가 0인 정점이 생기면 다시 이 과정을 반복하면서 정렬을 한다.