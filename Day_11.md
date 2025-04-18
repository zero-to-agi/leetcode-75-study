# Day 11: Backtracking

백트래킹(Backtracking)은 가능한 모든 조합을 탐색하면서 제약 조건을 만족하지 않는 경우 이전 단계로 돌아가는 알고리즘 기법입니다. 오늘 다룰 세 문제는 모두 백트래킹의 대표적인 응용 사례입니다.

## 1. Permutations

### 문제 설명
서로 다른 정수로 이루어진 배열 `nums`가 주어질 때, 가능한 모든 순열(permutation)을 반환하는 문제입니다.

### 접근 방법
백트래킹을 사용하여 모든 가능한 순열을 생성합니다. 현재 위치에 올 수 있는 모든 숫자를 시도하면서 순열을 만들어갑니다.

```python
def permute(nums):
    result = []
    
    def backtrack(current, remaining):
        # 순열 완성
        if not remaining:
            result.append(current[:])
            return
        
        # 사용 가능한 모든 숫자 시도
        for i in range(len(remaining)):
            # 현재 숫자 선택
            current.append(remaining[i])
            
            # 재귀 호출 (나머지 숫자에서 현재 선택한 숫자 제외)
            backtrack(current, remaining[:i] + remaining[i+1:])
            
            # 백트래킹 (선택 취소)
            current.pop()
    
    backtrack([], nums)
    return result
```

### 최적화된 방법
방문 여부를 배열로 관리하여 매번 새 remaining 배열을 생성하지 않도록 최적화할 수 있습니다.

```python
def permute(nums):
    n = len(nums)
    result = []
    
    def backtrack(current, used):
        # 순열 완성
        if len(current) == n:
            result.append(current[:])
            return
        
        for i in range(n):
            # 이미 사용한 숫자면 건너뛰기
            if used[i]:
                continue
                
            # 현재 숫자 선택
            current.append(nums[i])
            used[i] = True
            
            # 다음 숫자 선택
            backtrack(current, used)
            
            # 백트래킹
            current.pop()
            used[i] = False
    
    backtrack([], [False] * n)
    return result
```

### 복잡도 분석
- 시간 복잡도: O(n * n!) - n! 개의 순열이 있고, 각 순열을 만들 때 O(n) 작업 필요
- 공간 복잡도: O(n * n!) - 결과 저장에 필요한 공간

## 2. Combination Sum

### 문제 설명
서로 다른 양의 정수로 이루어진 배열 `candidates`와 목표 합 `target`이 주어질 때, `candidates`에서 숫자를 조합하여 합이 `target`이 되는 모든 고유한 조합을 찾는 문제입니다. 같은 숫자는 여러 번 사용할 수 있습니다.

### 접근 방법
백트래킹을 사용하여 가능한 모든 조합을 탐색합니다. 각 후보 숫자에 대해 포함하거나 포함하지 않는 두 가지 경우를 고려합니다.

```python
def combinationSum(candidates, target):
    result = []
    
    def backtrack(start, current, remaining):
        # 목표 합에 도달
        if remaining == 0:
            result.append(current[:])
            return
        
        # 목표 합을 초과하거나 더 이상 고려할 후보가 없음
        if remaining < 0 or start == len(candidates):
            return
        
        # 현재 후보 여러 번 사용 가능
        for i in range(start, len(candidates)):
            # 현재 후보 선택
            current.append(candidates[i])
            
            # 같은 숫자를 또 선택할 수 있으므로 start는 i로 유지
            backtrack(i, current, remaining - candidates[i])
            
            # 백트래킹
            current.pop()
    
    backtrack(0, [], target)
    return result
```

### 최적화
효율성을 위해 `candidates` 배열을 정렬하고, 남은 값이 현재 후보보다 작으면 탐색을 중단할 수 있습니다.

```python
def combinationSum(candidates, target):
    candidates.sort()  # 정렬
    result = []
    
    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        
        for i in range(start, len(candidates)):
            # 현재 후보가 목표보다 크면 더 탐색할 필요 없음
            if candidates[i] > remaining:
                break
                
            current.append(candidates[i])
            backtrack(i, current, remaining - candidates[i])
            current.pop()
    
    backtrack(0, [], target)
    return result
```

### 복잡도 분석
- 시간 복잡도: O(2^t) - 여기서 t는 target/min(candidates)로, 가능한 모든 조합의 수에 비례
- 공간 복잡도: O(target/min(candidates)) - 재귀 호출 깊이

## 3. Subsets

### 문제 설명
서로 다른 정수로 이루어진 배열 `nums`가 주어질 때, 가능한 모든 부분 집합(subset)을 반환하는 문제입니다.

### 접근 방법 1: 백트래킹
모든 원소에 대해 포함하거나 포함하지 않는 두 가지 경우를 고려하는 백트래킹 방식입니다.

```python
def subsets(nums):
    result = []
    
    def backtrack(start, current):
        # 현재 부분집합 추가
        result.append(current[:])
        
        for i in range(start, len(nums)):
            # 현재 원소 포함
            current.append(nums[i])
            
            # 다음 원소부터 탐색
            backtrack(i + 1, current)
            
            # 백트래킹
            current.pop()
    
    backtrack(0, [])
    return result
```

### 접근 방법 2: 비트 마스크
n개 원소의 모든 부분집합은 2^n개입니다. 각 비트가 원소의 포함 여부를 나타내는 비트 마스크를 사용할 수 있습니다.

```python
def subsets(nums):
    n = len(nums)
    result = []
    
    # 0부터 2^n-1까지 모든 경우 확인
    for i in range(1 << n):  # 1 << n은 2^n
        subset = []
        for j in range(n):
            # j번째 비트가 1이면 j번째 원소 포함
            if i & (1 << j):
                subset.append(nums[j])
        result.append(subset)
    
    return result
```

### 접근 방법 3: 반복적 구성
빈 집합에서 시작하여 하나씩 원소를 추가해가는 방식입니다.

```python
def subsets(nums):
    result = [[]]  # 빈 집합으로 시작
    
    for num in nums:
        # 기존 모든 부분집합에 현재 원소를 추가한 새 부분집합 생성
        result += [subset + [num] for subset in result]
    
    return result
```

### 복잡도 분석
- 시간 복잡도: O(n * 2^n) - 2^n개의 부분집합, 각 부분집합은 O(n) 작업 필요
- 공간 복잡도: O(n * 2^n) - 결과 저장에 필요한 공간

## 백트래킹의 일반적인 패턴

모든 백트래킹 문제는 다음 패턴을 따릅니다:

1. **재귀 함수 정의**: 현재 상태와 남은 선택지를 매개변수로 받음
2. **종료 조건 확인**: 해를 찾았거나 더 이상 탐색할 필요가 없는 경우
3. **선택과 탐색**: 가능한 모든 선택지에 대해 진행
4. **백트래킹**: 이전 상태로 되돌아가고 다른 선택지 탐색

이 세 문제를 통해 조합 문제와 백트래킹의 기본 패턴을 익힐 수 있습니다. 효율적인 구현을 위해 가지치기(pruning)와 상태 관리에 주의하는 것이 중요합니다.
