# Chapter 17
## Q58) Sort List
연결 리스트를 *O*(*n* log *n*)에 정렬하라.
* 입력
```
4->2->1->3
```
* 출력
```
1->2->3->4
```
### Sol1) 병합 정렬

입력값 -1->5->3->4->0 연결 리스트의 병합 정렬 과정 도식화

1) 중앙을 분할(Divide)
* 병합 정렬의 분할 정복(Divide and Conquer)을 위해서
* 5를 기준으로 분할하는 과정
* 런너(Runner) 기법 활용 <- 연결 리스트는 전체 길이를 알 수 없기 때문

```python
# 런너 기법 활용
half, slow, fast = None, head, head
while fast and fast.next:
    half, slow, fast = slow, slow.next, fast.next.next
half.next = None
```
* half, slow, fast 3개 변수 만듦
* slow는 한 칸씩, fast는 두 칸씩 앞으로 이동   
-> fast가 맨 끝에 도달했을 때 slow는 중앙에 도착
* half는 slow의 바로 이전 값으로 함
* 마지막에는 half.next = None으로 half의 위치를 기준으로 연결 리스트 관계를 끊어버림
* 5가 half가 된다.

2) 재귀 호출 진행
```python
# 분할 재귀 호출
def sortList(self, head: ListNode) -> ListNode:
    ...
    
    l1 = self.sortList(head)
    l2 = self.sortList(slow)
```
* head는 시작 노드, slow는 우리가 탐색을 통해 발견한 중앙 지점
* 3이 slow가 된다.
* 이 값을 기준으로 계속 재귀 호출을 해나가면 결국 연결 리스트는 -1,5,3,4,0 단일 아이템으로 모두 쪼개짐

3) 쪼갰던 아이템 다시 합쳐서 리턴
```python
def sortList(self, head: ListNode) -> ListNode:
    ...
    return self.mergeTwoLists(l1, l2)
```

크기 비교를 통해 정렬하면서 이어 붙임
```python
def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
    if l1 and l2:
        if l1.val > l2.val:
            l1, l2 = l2, l1
        l1.next = self.mergeTwoLists(l1.next, l2)
        
    return l1 or l2
```

* mergeTwoLists() 메소드는 연결 리스트를 입력값으로 받아, 길이가 얼마가 되든 재귀 호출을 통해 l1의 포인터를 이동하면서 정렬해 리턴한다.
* 여기서 return l1 or l2는 l1에 값이 있다면 항상 l1을 리턴하고, l1이 None인 경우 l2를 리턴한다. (즉, l1이 우선)

* 8장 14번 '두 정렬 리스트 병합'
* l1 or l2를 비교하지 않고 l1이 None이라면 미리 스왑해버리는 방식
```python
def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
    if (not l1) or (l2 and l1.val > l2.val):
        l1, l2 = l2, l1
    if l1:
        l1.next = self.mergeTwoLists(l1.next, l2)
    return l1
```

* 코드에서 처음에는 l1에 -1, l2에는 0이 입력값으로 들어오고, l1의 next인 5는 0보다 크기 때문에 스왑한다
* 이후에는 스왑 없이 계속 next 순서로 3, 4로 연결된다.
* 마지막에는 4의 next가 None이고, 이 경우 return l1 or l2 부분에서 l2 값이 리턴되며, 즉 5가 리턴된다.
* 재귀 호출된 부분들을 거슬러서 풀어보면 -1->0->3->4->5.

__전체 코드__
```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None

class Solution:
    # 두 정렬 리스트 병합
    def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
        if l1 and l2:
            if l1.val > l2.val:
                l1, l2 = l2, l1
            l1.next = self.mergeTwoLists(l1.next, l2)

        return l1 or l2

    def sortList(self, head: ListNode) -> ListNode:
        if not (head and head.next):
            return head

        # 런너 기법 활용
        half, slow, fast = None, head, head
        while fast and fast.next:
            half, slow, fast = slow, slow.next, fast.next.next
        half.next = None

        # 분할 재귀 호출
        l1 = self.sortList(head)
        l2 = self.sortList(slow)

        return self.mergeTwoLists(l1, l2)
```

### Sol3) 내장 함수를 이용하는 실용적인 방법
1) 연결 리스트를 파이썬의 리스트로 만들기
```python
list: List = []
while p:
    lst.append(p.val)
    p = p.next
```
2) 파이썬의 내장 정렬 함수인 sort()를 수행
```python
lst.sort()
```
3) 정렬한 리스트는 다시 연결 리스트로 만듦
```python
p = head
for i in range(len(lst)):
    p.val = lst[i]
    p = p.next
return head
```
* 여기서 p는 포인터.
* 연결 리스트를 순회하기 위한 포인터 변수를 p로 설정, 순회하며 값을 채워넣는 역할을 함
* 그런 다음 루트 노드 head를 리턴

__전체 코드__
```python
from typing import List


# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None


class Solution:
    def sortList(self, head: ListNode) -> ListNode:
        # 연결 리스트 -> 파이썬 리스트
        p = head
        lst: List = []
        while p:
            lst.append(p.val)
            p = p.next

        # 정렬
        lst.sort()

        # 파이썬 리스트 -> 연결 리스트
        p = head
        for i in range(len(lst)):
            p.val = lst[i]
            p = p.next
        return head
```

## Q61) Largest Number
항목들을 조합하여 만들 수 있는 가장 큰 수를 출력하라.

### Sol1) 삽입 정렬
* 각 요소 단위로 크기 순으로 정렬
* 맨 앞에서부터 자릿수 단위로 비교해서 크기 순으로 정렬
* 9는 30보다 맨 앞자리 수가 더 크므로 9가 더 앞에 와야 함
* 930이 큰지 309가 큰지 비교하는 문제로 풀 수 있음

1) a + b와 b + a를 비교하는 형태로 처리
```python
def to_swap(n1: int, n2: int) -> bool
    return str(n1) + str(n2) < str(n2) + str(n1)
```
2) 결과가 True라면 위치 변경 by 삽입 정렬
```python
def largestNumber(self, nums: List[int]) -> str:
    i = 1
    while i < len(nums):
        j = i
        while j > 0 and self.to_swap(nums[j - 1], nums[j]):
            nums[j], nums[j - 1] = nums[j - 1], nums[j]
            j -= 1
        i += 1
        ...
```
* 이전 값이 더 커서 스왑이 필요한지 여부를 체크하는 로직
* 단순 비교가 아닌 to_swap()이라는 함수를 통해 스왑 여부를 판별할 수 있도록 구현

__전체 코드__
```python
from typing import List

class Solution:
    # 문제에 적합한 비교 함수
    @staticmethod
    def to_swap(n1: int, n2: int) -> bool:
        return str(n1) + str(n2) < str(n2) + str(n1)

    # 삽입 정렬 구현
    def largestNumber(self, nums: List[int]) -> str:
        i = 1
        while i < len(nums):
            j = i
            while j > 0 and self.to_swap(nums[j - 1], nums[j]):
                nums[j], nums[j - 1] = nums[j - 1], nums[j]
                j -= 1
            i += 1

        return str(int(''.join(map(str, nums))))
```
* 리턴하는 부분 다소 번잡
* 원래 ''.join(map(str, nums)) 정도면 끝나게 되겠지만
* 여기서는 입력값이 ["0", "0"]인 경우도 있기 때문에 그냥 문자로 처리해버리면 리턴값이 00이 되기 때문
* join의 결과를 int로 바꿔서 00이 0이 되도록 만들어 준 후, 다시 str로 변경해서 그런 일이 없게 함

## Q64) K Closest Points to Origin
평면상에 points 목록이 있을 때, 원점(0, 0)에서 K번 가까운 점 목록을 순서대로 출력하라.

평면상 두 점의 거리는 유클리드 거리로 한다.

### Sol1) 유클리드 거리의 우선순위 큐 순서
__유클리드 거리(Euclidean Distance)__
유클리드 공간에서 두 점 사이의 거리를 계산하는 가장 '일반적인' 방법 중 하나

* 유클리드 거리 값을 크기가 작은 순으로 K번 추출하는 문제
* K번 추출에서 바로 우선순위 큐를 떠올릴 수 있음
* 즉 유클리드 거리를 계산하고 이 값을 우선순위 큐로 K번 출력하면 쉽게 문제 풀이 가능

1) math 모듈 사용-유클리드 거리 수식 구현&계산, 힙에 삽입
```python
heap = []
for (x, y) in points:
    dist = math.sqrt((0 - x) ** 2 + (0 - y) ** 2)
    heapq.heappush(heap, (dist, x, y))
```
* 우선순위 큐는 주로 힙으로 규현
* 파이썬의 heapq 모듈은 최소 힙(Min Heap)이기 때문에 거리가 가까운 순을 출력해야 하는 이 문제 풀이에 더욱 적합

2) 결과 추출
```python
result = []
for _ in range(K):
    (dist, x, y) = heapq.heappop(heap)
    result.append((x, y))
return result
```
* 결과 리스트에 힙에서 추출한 결과를 K번 삽입해 리턴하면 된다

__전체 코드__
```python
import heapq
from typing import List

class Solution:
    def kClosest(self, points: List[List[int]], K: int) -> List[List[int]]:
        heap = []
        for (x, y) in points:
            dist = x ** 2 + y ** 2
            heapq.heappush(heap, (dist, x, y))

        result = []
        for _ in range(K):
            (dist, x, y) = heapq.heappop(heap)
            result.append((x, y))
        return result
```
* 순서만 동일하면 되기 때문에 math.sqrt() 생략해도 
