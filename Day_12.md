# Day 12: Dynamic Programming

동적 계획법(Dynamic Programming, DP)은 복잡한 문제를 더 작은 하위 문제들로 나누어 해결하고, 하위 문제들의 결과를 저장하여 중복 계산을 방지하는 알고리즘 설계 기법입니다. 오늘은 DP의 기본 개념과 세 가지 대표적인 문제를 살펴보겠습니다.

## 1. Climbing Stairs

### 문제 설명
n개의 계단이 있을 때, 한 번에 1계단 또는 2계단씩 오를 수 있다면 꼭대기에 도달하는 서로 다른 방법의 수를 구하는 문제입니다.

### 접근 방법
이 문제의 핵심은 n번째 계단에 도달하는 방법은 다음 두 가지 경우의 합이라는 점입니다:
1. (n-1)번째 계단에서 1계단 올라옴
2. (n-2)번째 계단에서 2계단 올라옴

따라서 다음과 같은 점화식이 성립합니다:
```
dp[n] = dp[n-1] + dp[n-2]
```

이는 피보나치 수열과 동일한 형태입니다.

### 구현 방법 1: 메모이제이션(Top-down)

```python
def climbStairs(n):
    memo = {}
    
    def dp(i):
        if i <= 2:
            return i
        
        if i not in memo:
            memo[i] = dp(i-1) + dp(i-2)
        
        return memo[i]
    
    return dp(n)
```

### 구현 방법 2: 타뷸레이션(Bottom-up)

```python
def climbStairs(n):
    if n <= 2:
        return n
    
    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2
    
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    
    return dp[n]
```

### 공간 최적화 버전

```python
def climbStairs(n):
    if n <= 2:
        return n
    
    prev1 = 1  # dp[1]
    prev2 = 2  # dp[2]
    
    for i in range(3, n + 1):
        current = prev1 + prev2
        prev1 = prev2
        prev2 = current
    
    return prev2
```

### 복잡도 분석
- 시간 복잡도: O(n)
- 공간 복잡도: O(1) (최적화 버전)

## 2. House Robber

### 문제 설명
일렬로 배치된 집들에서 각 집에 있는 돈의 양이 배열로 주어집니다. 인접한 두 집을 연속으로 털면 경찰에 잡히게 됩니다. 훔칠 수 있는 최대 금액을 구하는 문제입니다.

### 접근 방법
각 위치 i에서 두 가지 선택이 가능합니다:
1. 현재 집을 털기 (이전 집은 털지 못함)
2. 현재 집을 털지 않기 (이전 집을 털 수도, 털지 않을 수도 있음)

이를 점화식으로 나타내면:
```
dp[i] = max(dp[i-2] + nums[i], dp[i-1])
```

### 구현 방법 1: 타뷸레이션(Bottom-up)

```python
def rob(nums):
    n = len(nums)
    
    # 예외 처리
    if n == 0:
        return 0
    if n == 1:
        return nums[0]
    
    dp = [0] * n
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    
    for i in range(2, n):
        dp[i] = max(dp[i-2] + nums[i], dp[i-1])
    
    return dp[n-1]
```

### 공간 최적화 버전

```python
def rob(nums):
    n = len(nums)
    
    # 예외 처리
    if n == 0:
        return 0
    if n == 1:
        return nums[0]
    
    prev2 = nums[0]  # dp[0]
    prev1 = max(nums[0], nums[1])  # dp[1]
    
    for i in range(2, n):
        current = max(prev2 + nums[i], prev1)
        prev2 = prev1
        prev1 = current
    
    return prev1
```

### 복잡도 분석
- 시간 복잡도: O(n)
- 공간 복잡도: O(1) (최적화 버전)

## 3. Longest Increasing Subsequence

### 문제 설명
정수 배열 nums가 주어졌을 때, 가장 긴 증가하는 부분 수열(subsequence)의 길이를 구하는 문제입니다.

### 접근 방법
각 위치 i에서 dp[i]는 인덱스 i까지 고려했을 때 가장 긴 증가하는 부분 수열의 길이를 나타냅니다.

점화식:
```
dp[i] = max(dp[j] + 1) for all j < i where nums[j] < nums[i]
```

### 구현 방법 1: 기본 DP 접근

```python
def lengthOfLIS(nums):
    if not nums:
        return 0
    
    n = len(nums)
    dp = [1] * n  # 모든 원소는 최소 길이 1의 부분 수열
    
    for i in range(1, n):
        for j in range(i):
            if nums[i] > nums[j]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)
```

### 구현 방법 2: 이진 탐색 최적화

이진 탐색을 사용한 O(n log n) 접근법도 가능합니다:

```python
def lengthOfLIS(nums):
    if not nums:
        return 0
    
    # tails[i]는 길이가 i+1인 증가 부분 수열의 마지막 원소 중 가장 작은 값
    tails = []
    
    for num in nums:
        # 이진 탐색으로 num이 들어갈 위치 찾기
        left, right = 0, len(tails)
        while left < right:
            mid = (left + right) // 2
            if tails[mid] < num:
                left = mid + 1
            else:
                right = mid
        
        # num이 들어갈 위치가 tails의 길이와 같다면 새로운 부분 수열 발견
        if left == len(tails):
            tails.append(num)
        # 그렇지 않다면 기존 위치의 값 갱신
        else:
            tails[left] = num
    
    return len(tails)
```

### 복잡도 분석
- 시간 복잡도:
  - 기본 DP: O(n²)
  - 이진 탐색 최적화: O(n log n)
- 공간 복잡도: O(n)

## DP 문제 해결 전략

동적 계획법 문제를 해결할 때 다음과 같은 단계를 따르면 도움이 됩니다:

1. **상태 정의**: 해결하려는 하위 문제가 무엇인지, dp[i]가 무엇을 의미하는지 명확히 정의합니다.
2. **점화식 설정**: 하위 문제들이 어떻게 현재 문제와 연결되는지 수식으로 나타냅니다.
3. **기저 사례 처리**: 가장 작은 하위 문제에 대한 해결책을 정의합니다.
4. **구현 방식 선택**: Top-down(재귀 + 메모이제이션) 또는 Bottom-up(반복문 + 배열) 방식을 선택합니다.
5. **최적화**: 공간 복잡도를 개선할 수 있는지 확인합니다.

## 요약

1. **Climbing Stairs**: 피보나치 수열과 유사한 전형적인 DP 문제로, 간단한 점화식을 통해 해결합니다.
2. **House Robber**: 인접 원소를 선택할 수 없는 제약 조건에서의 최대값 문제로, "선택 또는 비선택" 구조를 가집니다.
3. **Longest Increasing Subsequence**: 부분 수열 문제로, 단순 DP와 이진 탐색을 활용한 최적화 두 가지 방식으로 접근할 수 있습니다.

