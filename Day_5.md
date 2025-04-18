### Day 5 문제 해설 및 답안

### 1. Valid Parentheses
- **문제 설명**: 주어진 문자열 `s`가 유효한 괄호 문자열인지 확인하는 문제입니다. 유효한 괄호 문자열이란, 작성된 괄호가 모두 짝을 이루고 열린 괄호가 닫힌 괄호와 올바른 순서를 이루는 것을 의미합니다.

- **예제**:
  - **입력**: `s = "()"`
  - **출력**: `True`

- **해설**: 스택을 사용하여 열린 괄호는 스택에 넣고, 닫힌 괄호가 나타날 때마다 스택의 가장 위에 있는 괄호와 비교하여 짝이 맞는지 확인합니다. 모든 문자를 처리한 후 스택이 비어 있으면 유효한 괄호 문자열입니다.

- **코드**:
  ```python
  def isValid(s):
      stack = []
      mapping = {")": "(", "}": "{", "]": "["}
      
      for char in s:
          if char in mapping:
              top_element = stack.pop() if stack else '#'
              if mapping[char] != top_element:
                  return False
          else:
              stack.append(char)
      
      return not stack
  ```

---

### 2. Implement Queue using Stacks
- **문제 설명**: 두 개의 스택을 사용하여 큐를 구현하는 문제입니다. 큐는 FIFO(First In First Out) 형태인데, 스택은 LIFO(Last In First Out) 형태입니다. 이를 해결하기 위해 두 개의 스택을 사용합니다.

- **예제**:
  - **입력**: `enqueue(1)`, `enqueue(2)`, `dequeue()`
  - **출력**: `1` (첫 번째 넣은 값인 1이 출력)

- **해설**: 하나의 스택을 'main stack'으로 사용하고, 다른 하나를 'temp stack'으로 사용합니다. `enqueue` 시에는 `main stack`에 추가하고, `dequeue` 시 `main stack`에서 모든 요소를 `temp stack`으로 옮겨 역순으로 꺼내어 반환합니다.

- **코드**:
  ```python
  class MyQueue:
      def __init__(self):
          self.main_stack = []
          self.temp_stack = []

      def push(self, x: int) -> None:
          self.main_stack.append(x)

      def pop(self) -> int:
          self._transfer()
          return self.temp_stack.pop()

      def peek(self) -> int:
          self._transfer()
          return self.temp_stack[-1]

      def empty(self) -> bool:
          return not self.main_stack and not self.temp_stack

      def _transfer(self):
          if not self.temp_stack:  # Only transfer if temp_stack is empty
              while self.main_stack:
                  self.temp_stack.append(self.main_stack.pop())
  ```

---

### 3. Daily Temperatures
- **문제 설명**: 정수 배열 `T`가 주어질 때, 각 날의 온도에 대해 다음 더 따뜻한 날까지의 일수를 반환하는 문제입니다. 더 따뜻한 날이 없는 경우 0을 반환합니다.

- **예제**:
  - **입력**: `T = [73, 74, 75, 71, 69, 72, 76, 73]`
  - **출력**: `[1, 1, 4, 2, 1, 1, 0, 0]`

- **해설**: 스택을 사용하여 온도 인덱스를 저장하고, 현재 온도보다 따뜻한 온도가 올 때까지의 일수를 계산합니다. 현재 온도가 더 따뜻한 날이 나타날 경우, 스택에서 해당 날수를 빼내어 결과 배열에 업데이트합니다.

- **코드**:
  ```python
  def dailyTemperatures(T):
      n = len(T)
      result = [0] * n
      stack = []

      for i in range(n):
          while stack and T[i] > T[stack[-1]]:
              idx = stack.pop()
              result[idx] = i - idx
          stack.append(i)

      return result
  ```
