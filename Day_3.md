### Day 3 문제 해설 및 답안

### 1. Valid Palindrome
- **문제 설명**: 문자열 `s`가 주어질 때, 알파벳과 숫자만 포함하는 부분 문자열이 팰린드롬인지 확인하는 문제입니다. 대문자와 소문자는 같게 취급합니다.

- **예제**:
  - **입력**: `s = "A man, a plan, a canal: Panama"`
  - **출력**: `True` (문자열은 팰린드롬입니다)

- **해설**: 두 개의 포인터를 사용하여 문자열의 앞과 뒤에서 동시에 문자를 검사합니다. 알파벳과 숫자가 아닐 경우 포인터를 이동시키며, 최종적으로 맞지 않는 문자가 발견되면 팰린드롬이 아닙니다.

- **코드**:
  ```python
  def isPalindrome(s):
      left, right = 0, len(s) - 1
      
      while left < right:
          # Find next valid character from the left
          while left < right and not s[left].isalnum():
              left += 1
          # Find next valid character from the right
          while left < right and not s[right].isalnum():
              right -= 1
          
          # Compare characters
          if s[left].lower() != s[right].lower():
              return False
          
          left += 1
          right -= 1
      
      return True
  ```

---

### 2. Container With Most Water
- **문제 설명**: 정수 배열 `height`가 주어질 때, 두 개의 수를 선택하여 그 두 수 사이에 물을 담을 수 있는 최대 용적을 계산하는 문제입니다. 두 수의 최소값과 두 수의 간격을 곱하여 용적을 구합니다.

- **예제**:
  - **입력**: `height = [1,8,6,2,5,4,8,3,7]`
  - **출력**: `49` (높이 8과 7 사이의 거리 7로 인해 최대 물 용적)

- **해설**: 투 포인터 방법을 사용하여 왼쪽과 오른쪽 끝에서 포인터를 이동하며 최대 용적을 계산합니다. 더 높은 쪽의 포인터를 이동시키는 것이 최적입니다.

- **코드**:
  ```python
  def maxArea(height):
      left, right = 0, len(height) - 1
      max_area = 0
      
      while left < right:
          # Calculate area
          width = right - left
          current_height = min(height[left], height[right])
          max_area = max(max_area, width * current_height)
          
          # Move the pointer of the shorter line
          if height[left] < height[right]:
              left += 1
          else:
              right -= 1
      
      return max_area
  ```

---

### 3. 3Sum
- **문제 설명**: 정수 배열 `nums`가 주어질 때, 합이 0인 세 개의 수를 찾아 리스트로 반환하는 문제입니다.

- **예제**:
  - **입력**: `nums = [-1, 0, 1, 2, -1, -4]`
  - **출력**: `[[ -1, -1, 2], [ -1, 0, 1]]`
  
- **해설**: 먼저 배열을 정렬한 후, 각 숫자에 대해 두 개의 포인터를 사용하여 다른 두 수가 현재 숫자와 합이 0이 되는지 확인합니다. 이를 통해 중복된 수를 방지하고 O(n^2) 시간 복잡도로 해결할 수 있습니다.

- **코드**:
  ```python
  def threeSum(nums):
      nums.sort()
      result = []
      
      for i in range(len(nums) - 2):
          # Skip the same number to avoid duplicates
          if i > 0 and nums[i] == nums[i - 1]:
              continue
          
          left, right = i + 1, len(nums) - 1
          while left < right:
              total = nums[i] + nums[left] + nums[right]
              if total < 0:
                  left += 1
              elif total > 0:
                  right -= 1
              else:
                  result.append([nums[i], nums[left], nums[right]])
                  # Skip the same number to avoid duplicates
                  while left < right and nums[left] == nums[left + 1]:
                      left += 1
                  while left < right and nums[right] == nums[right - 1]:
                      right -= 1
                  left += 1
                  right -= 1
      
      return result
  ```
