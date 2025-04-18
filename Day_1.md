## Day 1 문제 해설 및 답안

### 1. Two Sum
- **문제 설명**: 배열 `nums`와 정수 `target`이 주어질 때, `nums`에서 두 개의 수를 찾아 그 합이 `target`과 같은 인덱스를 반환하는 문제입니다. 같은 인덱스의 수를 두 번 사용할 수는 없습니다.
  
- **예제**:
  - **입력**: `nums = [2, 7, 11, 15], target = 9`
  - **출력**: `[0, 1]` (nums[0] + nums[1] = 9)
  
- **해설**: 해시맵을 사용하여 각 수의 인덱스를 저장하고, 현재 숫자를 방문할 때 `target - num`이 해시맵에 존재하는지를 확인하면 효율적으로 두 수를 찾을 수 있습니다.

- **코드**:
  ```python
  def twoSum(nums, target):
      hashmap = {}
      for i, num in enumerate(nums):
          complement = target - num
          if complement in hashmap:
              return [hashmap[complement], i]
          hashmap[num] = i
  ```

---

### 2. Valid Anagram
- **문제 설명**: 두 개의 문자열 `s`와 `t`가 주어질 때, 두 문자열이 애너그램인지 확인하는 문제입니다. 애너그램이란, 두 문자열이 문자 빈도 수가 동일한 경우를 말합니다.

- **예제**:
  - **입력**: `s = "anagram", t = "nagaram"`
  - **출력**: `True`
  
- **해설**: 각 문자열의 문자 빈도를 계산한 뒤, 두 빈도가 같은지 확인하면 애너그램 여부를 알 수 있습니다.

- **코드**:
  ```python
  from collections import Counter

  def isAnagram(s, t):
      return Counter(s) == Counter(t)
  ```

---

### 3. Group Anagrams
- **문제 설명**: 문자열 배열 `strs`가 주어질 때, 애너그램 그룹을 묶어서 반환하는 문제입니다. 애너그램은 문자들의 조합이 같으므로, 소팅을 통해 같은 애너그램끼리 묶을 수 있습니다.

- **예제**:
  - **입력**: `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]`
  - **출력**: `[["bat"], ["nat", "tan"], ["ate", "eat", "tea"]]`
  
- **해설**: 각 문자열을 소팅한 후, 소팅된 문자열을 키로 하여 해시맵에 그룹화한 후 최종적으로 그룹들을 반환합니다.

- **코드**:
  ```python
  from collections import defaultdict

  def groupAnagrams(strs):
      anagrams = defaultdict(list)
      for s in strs:
          key = ''.join(sorted(s))
          anagrams[key].append(s)
      return list(anagrams.values())
  ```
