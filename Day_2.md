### Day 2 문제 해설 및 답안

### 1. Contains Duplicate
- **문제 설명**: 정수 배열 `nums`가 주어질 때, 배열 내에 중복된 요소가 존재하는지 확인하는 문제입니다.

- **예제**:
  - **입력**: `nums = [1, 2, 3, 1]`
  - **출력**: `True`
  
- **해설**: 해시셋을 이용하여 각 숫자를 저장하면서, 이미 존재하는 숫자가 발견되면 `True`를 반환합니다. 배열을 순회하면서 O(n) 시간 복잡도로 확인할 수 있습니다.

- **코드**:
  ```python
  def containsDuplicate(nums):
      seen = set()
      for num in nums:
          if num in seen:
              return True
          seen.add(num)
      return False
  ```

---

### 2. Product of Array Except Self
- **문제 설명**: 정수 배열 `nums`가 주어질 때, 각 인덱스에 대해 해당 인덱스를 제외한 배열의 곱을 계산하여 새로운 배열을 반환하는 문제입니다.

- **예제**:
  - **입력**: `nums = [1, 2, 3, 4]`
  - **출력**: `[24, 12, 8, 6]`
  
- **해설**: 두 가지 접근 방식이 있습니다.
  1. **곱셈을 사용한 방법**: 왼쪽과 오른쪽의 누적 곱을 저장하여 최종 곱을 계산합니다. 
  2. **나누셈을 사용한 방법**: 전체 배열 곱을 계산한 후 각 원소로 나누는 방법이지만, Zero가 포함되어 있을 경우 오류를 발생시키므로 안전하지 않습니다.

- **코드**:
  ```python
  def productExceptSelf(nums):
      n = len(nums)
      answer = [1] * n
      
      # 왼쪽 곱
      left = 1
      for i in range(n):
          answer[i] = left
          left *= nums[i]
      
      # 오른쪽 곱
      right = 1
      for i in range(n - 1, -1, -1):
          answer[i] *= right
          right *= nums[i]
      
      return answer
  ```

---

### 3. Maximum Subarray
- **문제 설명**: 정수 배열 `nums`가 주어질 때, 연속된 서브배열의 최대 합을 찾아 반환하는 문제입니다. 

- **예제**:
  - **입력**: `nums = [-2,1,-3,4,-1,2,1,-5,4]`
  - **출력**: `6` (서브배열 `[4,-1,2,1]`의 합)

- **해설**: 카데인 알고리즘을 사용하여 연속된 서브배열의 합을 계산합니다. 매 단계마다 최대 합을 업데이트하며, 현재 합이 음수일 경우 0으로 초기화합니다.

- **코드**:
  ```python
  def maxSubArray(nums):
      max_sum = current_sum = nums[0]
      for num in nums[1:]:
          current_sum = max(num, current_sum + num)
          max_sum = max(max_sum, current_sum)
      return max_sum
  ```
