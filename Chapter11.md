Chapter 11    
===========
## Q28) 해시맵 디자인(p.291)
```
다음의 기능을 제공하는 해시맵을 디자인하라.
* put(key, value): 키, 값을 해시맵에 삽입한다. 만약 이미 존재하는 키라면 업데이트한다.
* get(key): 키에 해당하는 값을 조회한다. 만약 키가 존재하지 않는다면 -1을 리턴한다.
* remove(key): 키에 해당하는 키, 값을 해시맵에서 삭제한다.

MyHashMap hashMap = new MyHashMap();
hashMap.put(1, 1);
hashMap.put(2, 2);
hashMap.get(1);            // 1을 리턴한다
hashMap.get(3);            // -1을 리턴한다(키가 존재하지 않음)
hashMap.put(2, 1);         // 값을 업데이트한다
hashMap.get(2);            // 1을 리턴한다
hashMap.remove(2);         // 키 2에 해당하는 키, 값을 삭제한다
hashMap.get(2);            // -1을 리턴한다(키가 삭제되어 존재하지 않음)
```
### Sol1) 개별 체이닝 방식을 이용한 해시 테이블 구현
```python
# 키, 값을 보관하고 연결 리스트로 처리할 별도의 객체 구현
class ListNode:
    def __init__(self, key=None, value=None):              # 각각이 None인 ListNode가 조회 즉시 
        self.key = key
        self.value = value
        self.next= None

class MyHashMap:
    # 초기화
    def __init__(Self):
        self.size = 1000                                   # 기본 사이즈는 1000개 정도로 적당히 설정
        self.table = collections.defaultdict(ListNode)     # collections.defaultdict: 존재하지 않는 키를 조회할 경우 자동으로 디폴트를 생성해줌
                                                           # defaultdict: 존재하지 않는 인덱스로 조회를 시도할 경우 에러를 발생하지 않고 그 자리에서 바로 디폴트 객체를 생성
    
    # 삽입
    def put(self, key: int, value: int) -> None:           # 편의상 모든 키를 정수형으로 지정
        index = key % self.size                            # size의 개수만큼 modulo연산을 한 나머지를 해시값으로 정함; modulo연산을 통한 해싱은 해시 테이블의 가장 기본적인 해싱 방식
        # 인덱스에 노드가 없다면 삽입 후 종료
        if self.table[index].value is None:                # 초기화 메소드 __init__()에서 선언한 self.table이 collections.defaultdict(ListNode)이기 때문에
                                                           # self.table[index].value로 value의 유무를 비교 -> 존재하지 않는 인덱스 조회할 경우 바로 빈 ListNode 생성
            self.table[index] = ListNode(key, value)
            return
        
        # 인덱스에 노드가 존재하는 경우 연결 리스트 처리
        p = self.table[index]
        while p:
            if p.next is None:                             # p.next is None 아무것도 하지 않고 루프를 빠져나감; 이게 없다면 p = p.next로 인해 p가 None이 되어 다음 코드에서 에러 발생
                break
            p = p.next
        p.next = ListNode(key, value)                      # p.next에 새 노드가 생성되면서 연결됨
        
    # 조회
    def get(self, key: int) -> int:
        index = key % self.size                            # 모듈로 연산으로 인덱스 결정
        if self.table[index].value is None:                # 해당하는 노드는 물론, 아직 어떠한 키도 이 값으로 해싱되지 않은 경우 -1 리턴
            return -1
        
        # 노드가 존재할 때 일치하는 키 탐색
        p = self.table[index]
        while p:
            if p.key == key:
                return p.value
            p = p.next                                     # p.next로 탐색하면서 일치하는 키 찾음
        return -1
    
    # 삭제
    def remove(self, key:int) -> None:
        index = key % self.size
        if self.table[index].value is None:                # 인덱스를 구한 다음 아무것도 없다면, 잘못된 키를 삭제 시도한 경우이므로 그냥 리턴
            return
        
        # 1) 인덱스의 첫 번째 노드일 때 삭제 처리
        p = self.table[index]
        if p.key == key:
            self.table[index] = ListNode() if p.next is None else p.next
            return
        
        # 2) 연결 리스트 노드 삭제
        prev = p
        while p:
            if p.key == key:
                prev.next = p.next
                return
            prev, p = p, p.next
```
## Q31) 상위 K 빈도 요소
```
k번 이상 등장하는 요소를 출력하라
* 입력
nums = [1,1,1,2,2,3], k = 2
* 출력
[1,2]
```
### Sol1) Counter를 이용한 음수 순 추출
* 우선순위 큐 이용, heapq 모듈 사용
```python
def topKFrequent(self, nums: List[int], k: int) -> List[int]:
    freqs = collections.Counter(nums)     # 빈도 수
    freqs_heap = []
    # 힙에 음수로 삽입
    for f in freqs:
        heapq.heappush(freqs_heap, (-freqs[f], f))    # heappush()로 삽입하게 되면 매번 heapify()가 일어나기 때문에 별도로 처리할 필요X
        # 빈도 수를 키로, freq의 키를 값으로 -> 키/값을 바꿔서 힙에 추가 bec 힙은 키 순서대로 정렬됨
    
    topk = list()
    # k번 만큼 추출, 최소 힙(Min Heap)이므로 가장 작은 음수 순으로 추출
    for _ in range(k):
        topk.append(heapq.heappop(freqs_heap)[1])
    
    return topk
```
### Sol2) 파이썬다운 방식
```python
def topKFrequent(self, nums, k):
    return list(zip(*collections.Counter(nums).most_common(k)))[0]
    # most_common(): Counter에서 제공하는 빈도 수가 높은 순서대로 아이템을 추출하는 기능
```
* zip() 함수: 2개 이상의 시퀀스를 짧은 길이를 기준으로 일대일 대응하는 새로운 튜플 시퀀스를 만드는 역할, 결과 자체는 튜플 시퀀스를 만들기 때문에 불변(Immutable) 객체
+ 아스테리스크 Asterisk(*): 시퀀스 언패킹 연산자(Sequence Unpacking Operator), 시퀀스를 풀어헤치는 연산자    
                         : 주로 튜플이나 리스트를 언패킹하는데 
