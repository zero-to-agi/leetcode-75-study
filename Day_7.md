# Day 7 문제 상세 해설

## 1. Min Stack

### 문제 설명
최소 스택(Min Stack)을 구현하는 문제입니다. 이 스택은 일반적인 스택의 모든 기능(push, pop, top)을 지원하면서 동시에 현재 스택 내의 최소값을 O(1) 시간 복잡도로 반환하는 `getMin()` 함수도 제공해야 합니다.

### 요구 사항
다음 작업을 지원하는 `MinStack` 클래스를 구현하세요:
- `MinStack()`: 최소 스택 객체를 초기화합니다.
- `void push(int val)`: 값을 스택에 푸시합니다.
- `void pop()`: 스택의 맨 위에 있는 요소를 제거합니다.
- `int top()`: 스택의 맨 위 요소를 가져옵니다.
- `int getMin()`: 스택에서 최소 요소를 검색합니다.

### 입력 예시
```
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]
```

### 출력 예시
```
[null,null,null,null,-3,null,0,-2]
```

### 접근 방법 상세 설명
이 문제의 핵심은 `getMin()` 함수를 호출할 때 O(1) 시간 복잡도로 최소값을 얻는 것입니다. 일반적인 스택 구현에서는 최소값을 찾기 위해 전체 스택을 순회해야 하므로 O(n) 시간이 필요합니다.

이를 해결하기 위한 접근법은 다음과 같습니다:

#### 방법 1: 두 개의 스택 사용하기
1. 하나의 스택은 모든 요소를 저장합니다(`stack`).
2. 다른 하나의 스택은 현재까지의 최소값을 저장합니다(`min_stack`).
3. 새 요소를 푸시할 때마다 현재 최소값과 비교하여 `min_stack`을 업데이트합니다.

#### 방법 2: 각 요소와 함께 현재 최소값 저장하기
1. 스택에 요소를 저장할 때, 요소의 값과 해당 시점의 최소값을 함께 튜플로 저장합니다.
2. 이렇게 하면 각 요소를 푸시하거나 팝할 때 현재 최소값을 항상 알 수 있습니다.

여기서는 첫 번째 방법을 사용하여 구현하겠습니다.

#### 알고리즘 단계별 설명
1. 두 개의 스택(`stack`과 `min_stack`)을 초기화합니다.
2. `push(val)` 함수:
   - `val`을 `stack`에 푸시합니다.
   - `min_stack`이 비어 있거나 `val`이 현재 최소값보다 작거나 같으면, `val`을 `min_stack`에도 푸시합니다.
3. `pop()` 함수:
   - `stack`에서 요소를 꺼냅니다.
   - 만약 꺼낸 요소가 현재 최소값과 같다면, `min_stack`에서도 요소를 꺼냅니다.
4. `top()` 함수:
   - `stack`의 맨 위 요소를 반환합니다.
5. `getMin()` 함수:
   - `min_stack`의 맨 위 요소를 반환합니다.

#### 코드 구현
```python
class MinStack:
    def __init__(self):
        self.stack = []       # 모든 요소를 저장하는 스택
        self.min_stack = []   # 최소값을 추적하는 스택

    def push(self, val: int) -> None:
        self.stack.append(val)
        
        # min_stack이 비어 있거나 val이 현재 최소값보다 작거나 같은 경우에만 추가
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)

    def pop(self) -> None:
        # 제거할 요소가 현재 최소값과 같으면 min_stack에서도 제거
        if self.stack[-1] == self.min_stack[-1]:
            self.min_stack.pop()
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.min_stack[-1]
```

#### 단계별 예시
입력:
```
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]
```

1. `MinStack()`: 스택을 초기화합니다. `stack = []`, `min_stack = []`
2. `push(-2)`: `-2`를 `stack`에 추가합니다. `stack = [-2]`
   - `min_stack`이 비어 있으므로 `-2`를 `min_stack`에도 추가합니다. `min_stack = [-2]`
3. `push(0)`: `0`을 `stack`에 추가합니다. `stack = [-2, 0]`
   - `0`은 현재 최소값 `-2`보다 크므로 `min_stack`에는 추가하지 않습니다. `min_stack = [-2]`
4. `push(-3)`: `-3`을 `stack`에 추가합니다. `stack = [-2, 0, -3]`
   - `-3`은 현재 최소값 `-2`보다 작으므로 `min_stack`에도 추가합니다. `min_stack = [-2, -3]`
5. `getMin()`: `min_stack`의 맨 위 요소 `-3`을 반환합니다.
6. `pop()`: `stack`에서 `-3`을 제거합니다. `stack = [-2, 0]`
   - `-3`은 현재 최소값이므로 `min_stack`에서도 제거합니다. `min_stack = [-2]`
7. `top()`: `stack`의 맨 위 요소 `0`을 반환합니다.
8. `getMin()`: `min_stack`의 맨 위 요소 `-2`를 반환합니다.

#### 시간 및 공간 복잡도
- **시간 복잡도**: 모든 작업(push, pop, top, getMin)이 O(1) 시간 복잡도를 가집니다.
- **공간 복잡도**: O(n) - 최악의 경우 모든 요소가 내림차순으로 들어오면 `min_stack`에도 모든 요소가 저장됩니다.

#### 최소 스택 구현의 핵심 포인트
이 문제의 핵심은 스택의 각 상태에서 최소값을 어떻게 효율적으로 추적할 것인가에 있습니다. 두 번째 스택을 사용하여 현재까지의 최소값 히스토리를 유지함으로써, O(1) 시간에 최소값을 조회할 수 있습니다.

또 다른 접근 방법으로는, 각 요소를 (값, 현재까지의 최소값) 튜플 형태로 저장하는 방법도 있습니다. 이 방법도 동일한 시간 복잡도를 가지지만, 공간 사용이 더 효율적일 수 있습니다.

## 2. Evaluate Reverse Polish Notation

### 문제 설명
역폴란드 표기법(RPN, Reverse Polish Notation)으로 표현된 수식을 평가하는 문제입니다. 역폴란드 표기법은 연산자가 피연산자 뒤에 오는 방식으로, 괄호 없이 연산 순서를 명확하게 표현할 수 있습니다.

문자열 토큰의 배열 `tokens`가 주어지면, 이를 평가한 결과를 반환해야 합니다. 유효한 연산자는 `+`, `-`, `*`, `/` 이며, 나눗셈 연산에서는 소수점 이하를 버립니다.

### 입력 예시
- `tokens = ["2","1","+","3","*"]`
- `tokens = ["4","13","5","/","+"]`
- `tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]`

### 출력 예시
- `tokens = ["2","1","+","3","*"]` → `9` ((2 + 1) * 3 = 9)
- `tokens = ["4","13","5","/","+"]` → `6` (4 + (13 / 5) = 4 + 2 = 6)
- `tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]` → `22`

### 접근 방법 상세 설명
역폴란드 표기법은 스택을 사용하여 효율적으로 평가할 수 있습니다. 알고리즘의 핵심은 다음과 같습니다:

1. 토큰을 왼쪽에서 오른쪽으로 순회합니다.
2. 숫자를 만나면 스택에 푸시합니다.
3. 연산자를 만나면 스택에서 두 개의 숫자를 팝하여 연산을 수행하고, 결과를 다시 스택에 푸시합니다.
4. 최종적으로 스택에 남아 있는 단일 값이 수식의 결과입니다.

#### 알고리즘 단계별 설명
1. 빈 스택을 초기화합니다.
2. `tokens` 배열의 각 토큰을 처리합니다:
   - 토큰이 숫자이면 스택에 푸시합니다.
   - 토큰이 연산자이면:
     - 스택에서 두 번째 피연산자(second)를 팝합니다.
     - 스택에서 첫 번째 피연산자(first)를 팝합니다.
     - 연산자에 따라 계산을 수행합니다: `first (연산자) second`
     - 결과를 스택에 푸시합니다.
3. 최종적으로 스택에 남아 있는 값을 반환합니다.

#### 코드 구현
```python
def evalRPN(tokens):
    stack = []
    
    for token in tokens:
        if token in ["+", "-", "*", "/"]:
            # 연산자를 만나면 스택에서 두 개의 숫자를 꺼냅니다
            second = stack.pop()
            first = stack.pop()
            
            # 연산을 수행하고 결과를 스택에 다시 푸시합니다
            if token == "+":
                stack.append(first + second)
            elif token == "-":
                stack.append(first - second)
            elif token == "*":
                stack.append(first * second)
            elif token == "/":
                # Python의 나눗셈은 소수점 아래를 버리는 것이 아니라 가장 가까운 정수로 반올림하는 int()를 사용합니다
                # 따라서 0으로 향하도록 나눗셈을 수행하기 위해 int(first / second)를 사용합니다
                stack.append(int(first / second))
        else:
            # 숫자인 경우 스택에 푸시합니다
            stack.append(int(token))
    
    # 최종 결과는 스택의 유일한 값입니다
    return stack[0]
```

#### 단계별 예시
입력: `tokens = ["2","1","+","3","*"]`

1. 토큰 "2": 스택에 2를 푸시합니다. `stack = [2]`
2. 토큰 "1": 스택에 1을 푸시합니다. `stack = [2, 1]`
3. 토큰 "+": 스택에서 두 개의 값을 꺼내어 더하고 결과를 푸시합니다.
   - second = 1, first = 2
   - 2 + 1 = 3
   - `stack = [3]`
4. 토큰 "3": 스택에 3을 푸시합니다. `stack = [3, 3]`
5. 토큰 "*": 스택에서 두 개의 값을 꺼내어 곱하고 결과를 푸시합니다.
   - second = 3, first = 3
   - 3 * 3 = 9
   - `stack = [9]`
6. 최종 결과: 9

#### 시간 및 공간 복잡도
- **시간 복잡도**: O(n) - 각 토큰을 한 번씩 처리합니다.
- **공간 복잡도**: O(n) - 최악의 경우, 모든 토큰이 숫자일 때 스택에 모두 저장될 수 있습니다.

#### 역폴란드 표기법 평가의 핵심 포인트
역폴란드 표기법(후위 표기법)은 연산자가 피연산자 뒤에 오는 방식으로, 괄호 없이 연산 순서를 명확하게 표현할 수 있습니다. 이 표기법의 주요 장점은 다음과 같습니다:

1. 괄호가 필요하지 않아 표현이 간결합니다.
2. 스택을 사용하여 간단하게 평가할 수 있습니다.
3. 컴퓨터가 수식을 평가하기 쉬운 형태입니다.

이 문제는 스택의 기본 원리를 이해하고 있다면 직관적으로 해결할 수 있으며, 표현식 평가의 기본적인 메커니즘을 이해하는 데 도움이 됩니다.

## 3. Daily Temperatures

### 문제 설명
일일 온도를 나타내는 정수 배열 `temperatures`가 주어집니다. 각 날짜에 대해, 더 따뜻한 온도가 나타날 때까지 기다려야 하는 일수를 계산하여 배열로 반환해야 합니다. 만약 더 따뜻한 날이 없다면 해당 날짜에 대해 0을 반환합니다.

### 입력 예시
- `temperatures = [73,74,75,71,69,72,76,73]`
- `temperatures = [30,40,50,60]`
- `temperatures = [30,60,90]`

### 출력 예시
- `temperatures = [73,74,75,71,69,72,76,73]` → `[1,1,4,2,1,1,0,0]`
- `temperatures = [30,40,50,60]` → `[1,1,1,0]`
- `temperatures = [30,60,90]` → `[1,1,0]`

### 접근 방법 상세 설명
이 문제는 **단조 감소 스택(Monotonic Decreasing Stack)** 패턴을 사용하여 효율적으로 해결할 수 있습니다. 이 패턴은 현재 요소보다 클 때까지 스택에서 요소를 팝하는 방식으로 작동합니다.

#### 알고리즘 단계별 설명
1. `temperatures` 배열의 길이와 같은 크기의 결과 배열 `answer`를 0으로 초기화합니다.
2. 빈 스택을 생성합니다. 이 스택은 (인덱스, 온도) 쌍을 저장합니다.
3. `temperatures` 배열을 왼쪽에서 오른쪽으로 순회합니다:
   - 현재 온도가 스택 맨 위에 있는 온도보다 높으면, 스택에서 요소를 팝하고 결과 배열을 업데이트합니다:
     - 팝한 인덱스에 대해, 현재 인덱스와의 차이가 더 따뜻한 날까지 기다려야 하는 일수입니다.
   - 현재 (인덱스, 온도) 쌍을 스택에 푸시합니다.
4. 순회가 끝나면 스택에 남아 있는 모든 인덱스에 대한 값은 0입니다(더 따뜻한 날이 없음).

#### 코드 구현
```python
def dailyTemperatures(temperatures):
    n = len(temperatures)
    answer = [0] * n  # 결과 배열을 0으로 초기화
    stack = []  # (인덱스, 온도) 쌍을 저장할 스택
    
    for i in range(n):
        # 현재 온도가 스택 맨 위의 온도보다 높으면
        while stack and temperatures[i] > stack[-1][1]:
            prev_idx, _ = stack.pop()  # 스택에서 요소를 팝
            answer[prev_idx] = i - prev_idx  # 결과 배열 업데이트
        
        # 현재 (인덱스, 온도) 쌍을 스택에 푸시
        stack.append((i, temperatures[i]))
    
    return answer
```

#### 단계별 예시
입력: `temperatures = [73,74,75,71,69,72,76,73]`

1. 초기 상태: `answer = [0,0,0,0,0,0,0,0]`, `stack = []`
2. i=0, temperatures[0]=73:
   - 스택이 비어 있으므로 (0, 73)을 푸시합니다. `stack = [(0, 73)]`
3. i=1, temperatures[1]=74:
   - 74 > 73이므로 (0, 73)을 팝하고 answer[0] = 1 - 0 = 1로 설정합니다.
   - (1, 74)를 푸시합니다. `stack = [(1, 74)]`, `answer = [1,0,0,0,0,0,0,0]`
4. i=2, temperatures[2]=75:
   - 75 > 74이므로 (1, 74)를 팝하고 answer[1] = 2 - 1 = 1로 설정합니다.
   - (2, 75)를 푸시합니다. `stack = [(2, 75)]`, `answer = [1,1,0,0,0,0,0,0]`
5. i=3, temperatures[3]=71:
   - 71 < 75이므로 아무 것도 팝하지 않습니다.
   - (3, 71)을 푸시합니다. `stack = [(2, 75), (3, 71)]`
6. i=4, temperatures[4]=69:
   - 69 < 71이므로 아무 것도 팝하지 않습니다.
   - (4, 69)를 푸시합니다. `stack = [(2, 75), (3, 71), (4, 69)]`
7. i=5, temperatures[5]=72:
   - 72 > 69이므로 (4, 69)를 팝하고 answer[4] = 5 - 4 = 1로 설정합니다.
   - 72 > 71이므로 (3, 71)을 팝하고 answer[3] = 5 - 3 = 2로 설정합니다.
   - 72 < 75이므로 아무 것도 팝하지 않습니다.
   - (5, 72)를 푸시합니다. `stack = [(2, 75), (5, 72)]`, `answer = [1,1,0,2,1,0,0,0]`
8. i=6, temperatures[6]=76:
   - 76 > 72이므로 (5, 72)를 팝하고 answer[5] = 6 - 5 = 1로 설정합니다.
   - 76 > 75이므로 (2, 75)를 팝하고 answer[2] = 6 - 2 = 4로 설정합니다.
   - (6, 76)을 푸시합니다. `stack = [(6, 76)]`, `answer = [1,1,4,2,1,1,0,0]`
9. i=7, temperatures[7]=73:
   - 73 < 76이므로 아무 것도 팝하지 않습니다.
   - (7, 73)을 푸시합니다. `stack = [(6, 76), (7, 73)]`
10. 순회 종료, 최종 결과: `answer = [1,1,4,2,1,1,0,0]`

#### 시간 및 공간 복잡도
- **시간 복잡도**: O(n) - 각 온도를 한 번씩 처리하고, 각 온도가 스택에 한 번 들어가고 한 번 나옵니다.
- **공간 복잡도**: O(n) - 최악의 경우, 모든 온도가 내림차순이면 스택에 모든 온도가 저장될 수 있습니다.

#### 단조 스택 패턴의 핵심 포인트
단조 스택(Monotonic Stack) 패턴은 다음과 같은 문제에 매우 유용합니다:
1. "다음 더 큰 요소" 또는 "다음 더 작은 요소" 찾기
2. 히스토그램에서 가장 큰 직사각형 찾기
3. 온도 변화와 같은 시간 경과에 따른 값의 변화 추적

이 패턴의 핵심은 스택이 특정 순서(오름차순 또는 내림차순)를 유지하도록 요소를 추가하고 제거하는 것입니다. 이 문제에서는 온도가 감소하는 순서로 스택을 유지하여, 더 따뜻한 날을 효율적으로 찾을 수 있습니다.
