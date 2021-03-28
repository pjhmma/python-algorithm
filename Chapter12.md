# Chapter12
## Q34) 순열(Permutations)

서로 다른 정수를 입력받아 가능한 모든 순열을 리턴하라.
* 입력
```
[1,2,3]
```
* 출력
```
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### Sol1) DFS를 활용한 순열 생성
```python
def permute(self, nums: List[int]) -> List[List[int]]:
    results = []
    prev_elements = []
    
    def dfs(elements):
        # 리프 노드일 때 결과 추가
        if len(elements) == 0:
            results.append(prev_elements[:])
        
        # 순열 생성 재귀 호출
        for e in elements:
            next_elements = elements[:]
            next_elements.remove(e)
            
            prev_elements.append(e)
            dfs(next_elements)
            prev_elements.pop()
            
    dfs(nums)
    return reesults
```

### Sol2) itertools 모듈 사용
```python
def permute(self, nums: List[int]) -> List[List[int]]:
    return list(itertools.permutations(nums))
```
```python
def permute(self, nums: List[int]) -> List[List[int]]:
    return list(map(list, itertools.permutations(nums)))
```

## Q37) 부분 집합(Subsets)
모든 부분 집합을 리턴하라.
* 입력
```
nums = [1,2,3]
```
* 출력
```
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

### Sol1) 트리의 모든 DFS 결과
```python
def subsets(self, nums: List[int]) -> List[List[int]]:
    result = []
    
    def dfs(index, path):
        # 매번 결과 추가
        result.append(path)
         
        # 경로를 만들면서 DFS
        for i in range(index , len(nums)):
            dfs(i + 1, path + [nums[i]])
     
    dfs(0, [])
    return result
```
