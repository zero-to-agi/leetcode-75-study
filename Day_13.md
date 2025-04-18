# Day 13: Dynamic Programming (continued)

## 1. Coin Change

### 문제 설명
서로 다른 금액의 동전들이 주어졌을 때, 특정 금액 `amount`를 만들기 위해 필요한 최소 동전 개수를 구하는 문제입니다. 각 동전은 무한히 사용할 수 있습니다.

### 접근 방법
이 문제는 전형적인 동적 계획법 문제로, "최소 개수"를 구하는 최적화 문제입니다.

DP 상태 정의:
- `dp[i]` = 금액 `i`를 만들기 위해 필요한 최소 동전 개수

점화식:
- `dp[i] = min(dp[i], dp[i - coin] + 1)` for all `coin` in `coins` where `i - coin >= 0`

### 구현

```python
def coinChange(coins, amount):
    # 금액이 0인 경우 동전이 필요 없음
    if amount == 0:
        return 0
    
    # dp[i] = 금액 i를 만들기 위한 최소 동전 개수
    # 최댓값으로 초기화 (amount + 1은 불가능한 값)
    dp = [amount + 1] * (amount + 1)
    dp[0] = 0  # 금액 0은 동전 0개로 만들 수 있음
    
    # 각 금액에 대해 최소 동전 개수 계산
    for i in range(1, amount + 1):
        # 각 종류의 동전에 대해
        for coin in coins:
            # 현재 동전을 사용할 수 있는 경우
            if i >= coin:
                # 현재 동전을 사용하는 경우와 사용하지 않는 경우 중 최소값 선택
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    # amount를 만들 수 없는 경우
    if dp[amount] > amount:
        return -1
    
    return dp[amount]
```

### 복잡도 분석
- 시간 복잡도: O(amount * n), 여기서 n은 동전 종류의 수
- 공간 복잡도: O(amount)

## 2. Word Break

### 문제 설명
문자열 `s`와 사전 `wordDict`가 주어졌을 때, `s`를 사전에 있는 단어들로 분할할 수 있는지 여부를 확인하는 문제입니다.

### 접근 방법
특정 위치까지의 문자열이 단어로 분할 가능한지를 저장하는 DP 배열을 사용합니다.

DP 상태 정의:
- `dp[i]` = 문자열 `s`의 처음부터 i-1번째 문자까지가 단어로 분할 가능한지 여부 (boolean)

점화식:
- `dp[i] = dp[j] && s[j:i] in wordDict` for some `j < i`

### 구현

```python
def wordBreak(s, wordDict):
    n = len(s)
    # 단어 검색을 빠르게 하기 위해 집합으로 변환
    word_set = set(wordDict)
    
    # dp[i] = s의 처음부터 i-1번째 문자까지가 단어로 분할 가능한지 여부
    dp = [False] * (n + 1)
    dp[0] = True  # 빈 문자열은 항상 분할 가능
    
    for i in range(1, n + 1):
        for j in range(i):
            # j까지가 분할 가능하고, j부터 i-1까지의 부분 문자열이 사전에 있는 경우
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]
```

### 최적화
자주 사용되는 최적화 중 하나는 사전에 있는 단어의 최대 길이를 고려하는 것입니다.

```python
def wordBreak(s, wordDict):
    n = len(s)
    word_set = set(wordDict)
    
    # 최대 단어 길이 계산
    max_word_length = max(len(word) for word in wordDict) if wordDict else 0
    
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        # j를 i에서 최대 단어 길이만큼 뒤로 이동시키며 검사
        # 이렇게 하면 불필요한 검사를 줄일 수 있음
        for j in range(i - 1, max(i - max_word_length - 1, -1), -1):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    
    return dp[n]
```

### 복잡도 분석
- 시간 복잡도: O(n² * k), 여기서 n은 문자열 길이, k는 사전에서 단어를 찾는 평균 시간
- 최적화 버전: O(n * m * k), 여기서 m은 사전에 있는 단어의 최대 길이
- 공간 복잡도: O(n)

## 3. Unique Paths

### 문제 설명
m×n 그리드의 왼쪽 상단에서 출발하여 오른쪽 하단까지 도달하는 서로 다른 경로의 수를 구하는 문제입니다. 로봇은 아래쪽 또는 오른쪽으로만 이동할 수 있습니다.

### 접근 방법
각 위치에 도달할 수 있는 경로의 수를 계산합니다.

DP 상태 정의:
- `dp[i][j]` = 위치 (i, j)에 도달하는 서로 다른 경로의 수

점화식:
- `dp[i][j] = dp[i-1][j] + dp[i][j-1]`
- 첫 번째 행과 첫 번째 열은 모두 1로 초기화 (각각 한 가지 방법으로만 도달 가능)

### 구현

```python
def uniquePaths(m, n):
    # dp[i][j] = 위치 (i, j)에 도달하는 서로 다른 경로의 수
    dp = [[0 for _ in range(n)] for _ in range(m)]
    
    # 첫 번째 행은 모두 1 (오른쪽으로만 이동 가능)
    for j in range(n):
        dp[0][j] = 1
    
    # 첫 번째 열은 모두 1 (아래쪽으로만 이동 가능)
    for i in range(m):
        dp[i][0] = 1
    
    # 나머지 셀 계산
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i-1][j] + dp[i][j-1]
    
    return dp[m-1][n-1]
```

### 공간 최적화
2차원 배열 대신 1차원 배열을 사용하여 공간을 최적화할 수 있습니다.

```python
def uniquePaths(m, n):
    # 한 행만 저장하면 충분
    dp = [1] * n
    
    # 각 행을 순차적으로 업데이트
    for i in range(1, m):
        for j in range(1, n):
            dp[j] += dp[j-1]
    
    return dp[n-1]
```

### 수학적 접근
이 문제는 조합론으로도 해결할 수 있습니다. 로봇이 목적지까지 가기 위해 "아래로 이동"을 m-1번, "오른쪽으로 이동"을 n-1번 해야 합니다. 
따라서 총 (m-1) + (n-1) = m+n-2번의 이동 중에서 m-1번의 "아래로 이동"을 선택하는 경우의 수를 구하는 것과 같습니다:

```python
def uniquePaths(m, n):
    # 조합 계산: C(m+n-2, m-1)
    # = (m+n-2)! / ((m-1)! * (n-1)!)
    
    # 분자와 분모 계산
    numerator = 1
    denominator = 1
    
    # min(m, n)을 사용하여 계산량 줄이기
    for i in range(1, min(m, n)):
        numerator *= (m + n - 1 - i)
        denominator *= i
    
    return numerator // denominator
```

### 복잡도 분석
- 시간 복잡도:
  - DP 접근: O(m * n)
  - 공간 최적화: O(m * n)
  - 수학적 접근: O(min(m, n))
- 공간 복잡도:
  - DP 접근: O(m * n)
  - 공간 최적화: O(n)
  - 수학적 접근: O(1)

## 동적 계획법 문제 해결 패턴

세 문제를 통해 동적 계획법의 다양한 패턴을 살펴볼 수 있습니다:

1. **최적화 문제 (Coin Change)**: 
   - 최소/최대값을 구하는 문제
   - 점화식에서 min/max 연산이 사용됨

2. **Yes/No 문제 (Word Break)**:
   - 가능성을 판단하는 문제
   - Boolean DP 배열 사용

3. **계수 문제 (Unique Paths)**:
   - 경우의 수를 세는 문제
   - 덧셈 연산을 통한 경우의 수 누적

## 문제 해결 팁

1. **상태 정의 명확히 하기**: `dp[i]`가 무엇을 의미하는지 정확히 정의합니다.
2. **점화식 도출**: 큰 문제와 작은 문제 사이의 관계를 수식으로 표현합니다.
3. **기저 조건 설정**: 가장 작은 문제에 대한 해결책을 정의합니다.
4. **반복 방향 결정**: 작은 문제부터 큰 문제로 (bottom-up) 또는 그 반대로 (top-down) 풀어갑니다.
5. **최적화 고려**: 공간 복잡도를 줄일 수 있는 방법이 있는지 확인합니다.
6. **테스트 케이스 검증**: 간단한 예제로 알고리즘을 검증합니다.

동적 계획법은 체계적인 접근이 중요합니다. 문제를 작은 단위로 나누고, 해결책을 점진적으로 구축해나가는 능력을 키우면 다양한 DP 문제를 효과적으로 해결할 수 있습니다.
