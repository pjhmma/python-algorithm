# Chapter 13, 14
## Q40) 네트워크 딜레이 타임
K부터 출발해 모든 노드가 신호를 받을 수 있는 시간을 계산하라. 불가능할 경우 -1을 리턴한다.
입력값으로 (u, v, w)는 각각 출발지, 도착지, 소요 시간으로 구성되며,
전체 노드의 개수는 N으로 입력받는다.
* 입력
```
times = [[2,1,1],[2,3,1],[3,4,1]], N = 4, K = 2
```
* 출력
```
2
```
### Sol1) 다익스트라 알고리즘 구현
```python
def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
    graph = collections.defaultdict(list)
    # 그래프 인접 리스트 구성
    for u, v, w in times:
        graph[u].append((u, v))
    
    # 큐 변수: [(소요 시간, 정점)]
    Q = [(0, K)]
    dist = collections.defaultdict(int)
    
    # 우선순위 큐 최솟값 기준으로 정점까지 최단 경로 삽입
    while Q:
        time, node = heapq.heappop(Q)
        if node not in dist:
            dist[node] = time
            for v, w in graph[node]:
                alt = time + w
                heapq.heappush(Q, (alt, v))
    
    # 모든 노드의 최단 경로 존재 여부 판별
    if len(dist) == N:
        return max(dist.values())
    return -1
```
## Q43) 이진 트리의 직경
이진 트리에서 두 노드 간 가장 긴 경로의 길이를 출력하라.
### Sol) 상태값 누적 트리 DFS
```python
class Solution:
    longest: int = 0
    
    def diameterOfBinaryTree(self, root: TreeNode) -> int:
        def dfs(node: TreeNode) -> int:
            if not node:
                return -1
            # 왼쪽, 오른쪽의 각 리프 노드까지 탐색
            left = dfs(node.left)
            right = dfs(node.right)
            
            # 가장 긴 경로
            self.longest = max(self.longest, left + right + 2)
            # 상태값
            return max(left, right) + 1
        
        dfs(root)
        return self.longest
```

## Q46) 두 이진 트리 병합
두 이진 트리를 병합하라. 중복되는 노드는 값을 합산한다.

### Sol)
```python
def mergeTrees(self, t1: TreeNode, t2: TreeNode) -> TreeNode:
    if t1 and t2:
        node = TreeNode(t1.val + t2.val)
        node.left = self.mergeTrees(t1.left, t2.left)
        node.right = self.mergeTrees(t1.right, t2.right)
        
        return node
    else:
        return t1 or t2
```

## Q49) 최소 높이 트리
노드 개수와 무방향 그래프를 입력받아 트리가 최소 높이가 되는 루트의 목록을 리턴하라.
* 입력
```
n =4, edges = [[1,0],[1,2],[1,3]]
```
* 출력
```
[1]
```
* 입력
```
n = 6, edges = [[0,3],[1,3],[2,3],[4,3],[5,4]]
```
* 출력
```
[3,4]
```
### Sol)
```python
import collections
from typing import List

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        if n <= 1:
            return [0]

        # 양방향 그래프 구성
        graph = collections.defaultdict(list)
        for i, j in edges:
            graph[i].append(j)
            graph[j].append(i)

        # 첫 번째 리프 노드 추가
        leaves = []
        for i in range(n + 1):
            if len(graph[i]) == 1:
                leaves.append(i)

        # 루트 노드만 남을 때까지 반복 제거
        while n > 2:
            n -= len(leaves)
            new_leaves = []
            for leaf in leaves:
                neighbor = graph[leaf].pop()
                graph[neighbor].remove(leaf)

                if len(graph[neighbor]) == 1:
                    new_leaves.append(neighbor)

            leaves = new_leaves

        return leaves
```
