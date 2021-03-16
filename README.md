# python-algorithm
## Q13) 팰린드롬 연결 리스트(p.201)
> 연결 리스트가 팰린드롬 구조인지 판별하라.
>	> * 입력    
>	> 1->2    
>	> * 출력    
>	> false    
>	> * 입력    
>	> 1->2->2->1    
>	> * 출력    
>	> true    
* 팰린드롬(Palindrome): 앞뒤가 똑 같은 단어나 문장, 뒤집어도 같은 말이 되는 단어 또는 문장
### 풀이1) 리스트 변환
* 팰린드롬 여부를 판별하기 위해서는 앞뒤로 모두 추출할 수 있는 자료구조 필요
* 파이썬 리스트 - pop() 함수에 인덱스 지정 가능 -> 마지막 요소 X라도 얼마든지 원하는 위치 자유롭게 추출 가능
* 연결 리스트를 파이썬의 리스트로 변환하여 리스트 기능 이용

```python
def isPalindrome(self, head: ListNone) -> bool:
    q: List = []
    
    if not head:
        return True
    
    node = head
    # 리스트 변환
    while node is not None:
        q.append(node.val)
        node = node.next
    
    # 팰린드롬 판별
    while len(q) > 1:
        if q.pop(0) != q.pop(): # q.pop(0): 리스트 첫번째 요소 추출, q.pop(): 리스트 마지막 요소 추출
            return False
    
    return True
```

### 풀이2) 데크를 이용한 최적화
* `q.pop(0)` 동적 배열로 구성된 리스트는 맨 앞 아이템을 가져오기에 적합한 자료형이 아님 bec 첫 번째 값 꺼내오면 모든 값이 한 칸씩 shifting되며, 시간 복잡도 *O*(n)이 발생    
-> 최적화를 위해서는 맨 앞에 데이터 가져올 때 *O*(n) 이내에 처리할 수 있는 자료형 필요
* 데크(Deque): 이중 연결 리스트 구조로 양쪽 방향 모두 추출하는 데 시간 복잡도 *O*(1)에 실행된다    
```python
def isPalindrome(self, head: ListNode) -> bool:
    # 데크 자료형 선언
    q: Deque = collections.deque()
    
    if not head:
        return True
    
    node = head
    while node is not None:
        q.append(node.val)
        node = node.next
    
    while len(q) > 1:
        if q.popleft() != q.pop(): # 데크에서 제공하는 메소드로 비교, popleft(): 덱의 가장 왼쪽에 있는 원소를 덱에서 제거하고, 그 값을 리턴
            return False
    
    return True
```

### 풀이3) 런너를 이용한 우아한 풀이
* 런너(Runner) 기법: 연결 리스트를 순회할 때 2개의 포인터를 동시에 사용하는 기법. 부가 포인터라고도 함    
    - 한 포인터가 다른 포인터보다 앞서게 하여 병합 지점이나 중간 위치, 길이 등 판별 시 사용    
    - 2개의 포인터 - 빠른 런너(Fast Runner): 대게 두 칸씩 건너뜀, 느린 런너(Slow Runner): 한 칸 씩 이동    
    - 빠른 런너가 연결 리스트 끝에 도달하면 느린 런너는 정확하게 연결 리스트의 중간 지점을 가리킴    
    - 성능을 높이면서 연결 리스트를 다른 자료형으로 변환하지 않고 풀어 연결 리스트답게 풀이 가능
```python
def isPalindrome(self, head: ListNode) -> bool:
    rev = None
    slow = fast = head           # 빠른 런너 fast와 느린 런너 slow의 초깃값은 모두 head에서 시작
    # 런너를 이용해 역순 연결 리스트 구성
    while fast and fast.next:   # 런너 이동: next가 존재하지 않을 때까지 이동
        fast = fast.next.next   # 빠른 런너 fast 두 칸씩 이동
        rev, rev.next, slow = slow, rev, slow.next   # 역순으로 연결 리스트 rev 생성 로직, 느린 런너 slow 한 칸씩 이동
    if fast:
        slow = slow.next
    
    # 팰린드롬 여부 확인
    while rev and rev.val == slow.val:
        slow, rev = slow.next, rev.next
    return not rev
```

## Q22) 일일 온도(p.252)
> 매일의 화씨 온도(℉) 리스트 T를 입력받아서, 더 따뜻한 날씨를 위해서는 며칠을 더 기다려야 하는지를 출력하라.
>	> * 입력    
>	> T = [73, 74, 75, 71, 69, 72, 76, 73]    
>	> * 출력    
>	> [1, 1, 4, 2, 1, 1, 0, 0]

### 풀이) 스택 값 비교
* ‘빗물 트래핑(p.180)’ 문제와 유사한 방법으로 풀이 가능
```python
def dailyTemperatures(self, T: List[int]) -> List[int]:
    answer = [0] * len(T)
    stack = []
    for i, cur in enumerate(T):
        while stack and cur > T[stack[-1]]:
            last = stack.pop()
            answer[last] = i - last
        stack.append(i)
    return answer
```
* 현재의 인덱스를 계속 스택에 쌓아두다가, 이전보다 상승하는 지점에서 현재 온도와 스택에 쌓아둔 인덱스 지점의 온도 차이를 비교해서, 더 높다면 스택의 값을 팝으로 꺼내고 현재 인덱스와 스택에 쌓아둔 인덱스의 차이를 정답으로 처리

## Q25) 원형 큐 디자인(p.259)
> 원형 큐를 디자인하라.    
>	> MyCircularQueue circularQueue = new MyCircularQueue(5); // 크기를 5로 지정    
>	> circularQueue.enQueue(10);   // true 리턴    
>	> circularQueue.enQueue(20);   // true 리턴    
>	> circularQueue.enQueue(30);   // true 리턴    
>	> circularQueue.enQueue(40);   // true 리턴    
>	> circularQueue.Rear();        // 40 리턴    
>	> circularQueue.isFull();      // false 리턴    
>	> circularQueue.deQueue();     // true 리턴    
>	> circularQueue.deQueue();     // true 리턴    
>	> circularQueue.deQueue(50);   // true 리턴    
>	> circularQueue.deQueue(60);   // true 리턴    
>	> circularQueue.Rear();        // 60    
>	> circularQueue.Front();       // 30 리턴    

### 풀이) 배열을 이용한 풀이
* 원형 큐(Circular Queue): FIFO(First-In-First-Out; 선입선출)구조를 지닌다는 점에서 기존의 큐와 동일하나 마지막 위치가 시작 위치와 연결되는 원형 구조를 띤다. 링 버퍼(Ring Buffer)라고도 부른다.
* 기존의 큐: 공간이 꽉 차면 더 이상 요소를 추가할 수 없으며 앞쪽의 요소들이 deQueue()로 모두 빠져서 충분한 공간이 남아도 그쪽으로는 추가할 방법 X
-> 원형 큐: 앞쪽에 공간이 남아 있다면 동그랗게 연결해 앞쪽으로 추가할 수 있도록 재활용 가능한 구조
* 동작하는 원리는 투 포인터와도 비슷; 마지막 위치와 시작 위치를 연결하는 원형 구조를 만들고, 요소의 시작점과 끝점을 따라 투 포인트가 움직인다. enQueue()를 하면 rear 포인터가 앞으로 이동하고, deQueue()를 하면 front 포인터가 앞으로 이동한다.
* 원형 큐 구현; 배열 사용
```pyhton
class MyCircularQueue:
    def __init__(self, k: int):
        self.q = [None] * k
        self.maxlen = k
        self.p1 = 0
        self.p2 = 0
        
    # enQueue() : rear 포인터 이동
    def enQueue(self, value: int) -> bool:
        if self.q[self.p2] is None:
            self.q[self,p2] = value
            self.p2 = (self.p2 + 1) % self.maxlen
            return True
        else:
            return False
        
    # deQueue(): front 포인터 이동
    def deQueue(self) -> bool:
        if self.q[self.p1] is None:
            return False
        else:
            self.q[self.p1] = None
            self.p1 = (self.p1 + 1) % self.maxlen
            return True
        
    def Front(self) -> int:
        return -1 if self.q[self.p1] is None else self.q[self.p1]
    
    def Rear(self) -> int:
        return -1 if self.q[self.p2 - 1] is None else self.q[self.p2 - 1]
    
    def isEmpty(self) -> bool:
        return self.p1 == self.p2 and self.q[self.p1] is None
    
    def isFull(self) -> bool:
        return self.p1 == self.p2 and self.q[self.p1] is not None
```
