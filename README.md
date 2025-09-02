# [내일배움캠프 언리얼5기] 그리디 알고리즘

## 그리디 알고리즘

각 단계에서 가능한 선택 중 최적의 선택을 하여

전체적으로 최적해를 찾는 것을 목표로 하는 알고리즘입니다.

그리디 알고리즘은 탐욕 선택 속성과 최적 부분 구조를

모두 만족하는 문제에 대해서만 최적해를 보장할 수 있습니다.

### 탐욕 선택 속성

각 단계에서의 지역적인 최적 선택이 전체 문제의 최적 해로

이어질 수 있는 속성이 탐욕 선택 속성입니다.

## 최소 신장 트리

신장 트리란 주어진 그래프에서 모든 정점을 포함하면서,

사이클 없이 하나의 연결된 구조를 그리는 부분 그래프입니다.

그 중 신장 트리 중 간선의 가중치 합이 최소인 신장 트리를 의미합니다.

### 프림 알고리즘

임의의 정점을 하나 선택해 최소 신장 트리에 추가하고,

현재 트리에서 연결 가능한 간선 중, 가중치가 가장 작은 간선을 선택하여

해당 간선의 반대편 정점을 트리에 추가합니다.

이 과정을 모든 정점이 트리에 포함될 때까지 반복하는 알고리즘입니다.

```
Prim(G):
    초기화:
        - 임의의 시작 정점 s 선택
        - 방문한 정점 집합 visited ← {s}
        - 후보 간선 집합 edges ← s에 인접한 모든 간선

    while visited에 포함되지 않은 정점이 남아있을 때:
        - edges에서 가중치가 가장 작은 간선 (u, v)를 선택
        - v가 visited에 없다면:
            - v를 visited에 추가
            - 간선 (u, v)를 최소 신장 트리에 추가
            - v에 인접한 간선들 중, 아직 방문하지 않은 정점으로 가는 간선을 edges에 추가

    최소 신장 트리를 반환
```
		- 프림 알고리즘의 의사 코드

```cpp

#include <iostream>
#include <vector>

using namespace std;

const int MAX = 100;
const int INF = 1000000000;

int cost[MAX][MAX];  // 인접 행렬: 간선의 비용 저장
bool visited[MAX];   // 방문 여부
int minEdge[MAX];    // 각 정점에 연결된 최소 비용 간선의 가중치
int parent[MAX];     // MST에서 각 정점의 부모 정점 인덱스

int main() {
    int V = 5;  // 정점의 개수

    // 간선 비용 초기화 (없는 간선은 INF)
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            cost[i][j] = INF;
        }
    }

    // 예시 간선들 (무방향 그래프)
    cost[0][1] = cost[1][0] = 2;
    cost[0][3] = cost[3][0] = 6;
    cost[1][2] = cost[2][1] = 3;
    cost[1][3] = cost[3][1] = 8;
    cost[1][4] = cost[4][1] = 5;
    cost[2][4] = cost[4][2] = 7;
    cost[3][4] = cost[4][3] = 9; // 다른 간선 예시

    // 방문 배열, 최소 간선 배열, 부모 배열 초기화
    for (int i = 0; i < V; i++) {
        visited[i] = false;
        minEdge[i] = INF;
        parent[i] = -1; // 부모 없음으로 초기화
    }

    minEdge[0] = 0;  // 시작 정점(0번) 설정

    // V개의 정점을 MST에 포함시킬 때까지 반복
    for (int count = 0; count < V; count++) {
        int minVal = INF;
        int u = -1;

        // 방문하지 않은 정점 중 MST에 가장 가까운 정점 u 찾기
        for (int j = 0; j < V; j++) {
            if (!visited[j] && minEdge[j] < minVal) {
                minVal = minEdge[j];
                u = j;
            }
        }

        if (u == -1) break; // 연결 그래프가 아닌 경우 대비

        visited[u] = true; // 정점 u 방문 처리

        // 새로 추가된 정점 u와 인접한, 아직 방문하지 않은 정점 v들의 정보 갱신
        for (int v = 0; v < V; v++) {
            if (!visited[v] && cost[u][v] != INF && cost[u][v] < minEdge[v]) {
                minEdge[v] = cost[u][v]; // 최소 비용 갱신
                parent[v] = u;           // v의 부모를 u로 설정
            }
        }
    }

    // 최소 신장 트리를 구성하는 간선 목록만 출력
    cout << "최소 신장 트리를 구성하는 간선 목록:" << endl;
    for (int i = 1; i < V; ++i) { // 루트(0번) 제외하고 확인
        if (parent[i] != -1) { // MST에 포함된 간선만 출력
             cout << "간선: (" << parent[i] << ", " << i << ") 비용: " << cost[parent[i]][i] << endl;
        }
    }

    return 0;
}
```
		- 프림 알고리즘의 얘시


### 크루스칼 알고리즘

그래프의 모든 간선을 기준으로 오름차순 정렬하고, 

가중치가 가장 작은 간선부터 하나씩 선택하여,

사이클이 발생하지 않으면 최소 신장 트리에 추가한다.

이 과정을 모든 정점이 하나의 트리로 연결될 때까지 반복합니다.

```
Kruskal(G):
    초기화:
        - 모든 정점을 각각의 집합으로 만든다 (Disjoint Set)
        - 간선들을 가중치 기준으로 오름차순 정렬
        - 최소 신장 트리 MST ← ∅

    for 각 간선 (u, v)를 정렬된 순서대로 확인:
        - u와 v가 서로 다른 집합에 속해 있다면:
            - 간선 (u, v)를 MST에 추가
            - u와 v의 집합을 합친다 (Union)

        - MST의 간선 개수가 (정점 수 - 1)이 되면 종료

    MST(최소 신장 트리)를 반환
```

		- 크루스칼 알고리즘의 의사 코드

```cpp

#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

const int MAX = 100;

struct Edge {
    int u, v, weight;
    bool operator<(const Edge& other) const {
        return weight < other.weight;
    }
};

int parent[MAX];

int find(int x) {
    if (parent[x] == x) return x;
    return parent[x] = find(parent[x]);
}

void unionSet(int x, int y) {
    x = find(x);
    y = find(y);
    if (x != y) {
        parent[y] = x;
    }
}

int main() {
    int V = 5;
    vector<Edge> edges;
    vector<Edge> mstEdges;

    edges.push_back({ 0, 1, 2 });
    edges.push_back({ 0, 3, 6 });
    edges.push_back({ 1, 2, 3 });
    edges.push_back({ 1, 3, 8 });
    edges.push_back({ 1, 4, 5 });
    edges.push_back({ 2, 4, 7 });
    edges.push_back({ 3, 4, 9 });

    for (int i = 0; i < V; i++) {
        parent[i] = i;
    }

    sort(edges.begin(), edges.end());

    int edgeCount = 0;
\
    for (Edge e : edges) {
        if (find(e.u) != find(e.v)) {
            unionSet(e.u, e.v);
            mstEdges.push_back(e);
            edgeCount++;
            if (edgeCount == V - 1) {
                break;
            }
        }
    }

    cout << "최소 신장 트리를 구성하는 간선 목록:" << endl;
    for (const Edge& e : mstEdges) {
        cout << "간선: (" << e.u << ", " << e.v << ") 비용: " << e.weight << endl;
    }

    return 0;
}
```

		- 크루스칼 알고리즘의 예시

#그리디 알고리즘 #내일배움캠프 #언리얼5기
