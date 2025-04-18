### Day 4 문제 해설 및 답안

### 1. Longest Substring Without Repeating Characters
- **문제 설명**: 문자열 `s`가 주어질 때, 중복된 문자가 없는 가장 긴 부분 문자열의 길이를 반환하는 문제입니다.

- **예제**:
  - **입력**: `s = "abcabcbb"`
  - **출력**: `3` ("abc"가 중복 없이 가장 긴 부분 문자열입니다)

- **해설**: 슬라이딩 윈도우 기법을 사용하여 현재 문자와 이전에 나타난 문자의 인덱스를 저장합니다. 중복 문자가 발견되면 왼쪽 포인터를 이동하여 중복을 제거하고, 매 단계마다 최대 길이를 업데이트합니다.

- **코드**:
  ```python
  def lengthOfLongestSubstring(s):
      char_index = {}
      left = max_length = 0
      
      for right in range(len(s)):
          if s[right] in char_index and char_index[s[right]] >= left:
              left = char_index[s[right]] + 1
          char_index[s[right]] = right
          max_length = max(max_length, right - left + 1)
      
      return max_length
  ```

---

### 2. Minimum Window Substring
- **문제 설명**: 문자열 `s`와 `t`가 주어질 때, `t`의 모든 문자를 포함하는 `s`의 최소 부분 문자열을 찾는 문제입니다.

- **예제**:
  - **입력**: `s = "ADOBECODEBANCE", t = "ABC"`
  - **출력**: `"BANC"` (부분 문자열 "BANC"가 t의 모든 문자를 포함합니다)

- **해설**: 두 개의 포인터를 사용하여 현재 부분 문자열이 `t`의 모든 문자를 포함하는지 확인합니다. 포함 상태를 유지하기 위해 해시맵을 사용하고, 포함 상태를 만족할 경우 왼쪽 포인터를 이동시켜 최소 길이를 찾습니다.

- **코드**:
  ```python
  from collections import Counter

  def minWindow(s, t):
      if not s or not t:
          return ""
  
      dict_t = Counter(t)
      required = len(dict_t)
      l, r = 0, 0
      formed = 0
  
      window_counts = {}
      min_len = float("inf")
      min_left = 0
  
      while r < len(s):
          char = s[r]
          window_counts[char] = window_counts.get(char, 0) + 1
  
          if char in dict_t and window_counts[char] == dict_t[char]:
              formed += 1
  
          while l <= r and formed == required:
              char = s[l]
              if r - l + 1 < min_len:
                  min_len = r - l + 1
                  min_left = l
  
              window_counts[char] -= 1
              if char in dict_t and window_counts[char] < dict_t[char]:
                  formed -= 1
              l += 1
  
          r += 1
  
      return "" if min_len == float("inf") else s[min_left:min_left + min_len]
  ```

---

### 3. Permutation in String
- **문제 설명**: 문자열 `s1`과 `s2`가 주어질 때, `s2`가 `s1`의 애너그램인지 확인하는 문제입니다. 즉, `s2`에 `s1`의 문자들이 조합된 부분 문자열이 있는지를 찾는 것입니다.

- **예제**:
  - **입력**: `s1 = "ab", s2 = "eidbaooo"`
  - **출력**: `True` (s2의 부분 문자열 "ba"가 s1의 애너그램입니다)

- **해설**: 슬라이딩 윈도우 기법을 사용하여 `s1`의 길이만큼의 부분 문자열을 `s2`에서 탐색하고, `s1`의 문자 빈도 수와 일치하는지 확인합니다. 두 해시맵을 비교하여 애너그램 여부를 결정합니다.

- **코드**:
  ```python
  from collections import Counter

  def checkInclusion(s1, s2):
      len_s1, len_s2 = len(s1), len(s2)

      if len_s1 > len_s2:
          return False

      count_s1 = Counter(s1)
      count_s2 = Counter(s2[:len_s1])

      if count_s1 == count_s2:
          return True

      for i in range(len_s1, len_s2):
          count_s2[s2[i]] += 1
          count_s2[s2[i - len_s1]] -= 1

          if count_s2[s2[i - len_s1]] == 0:
              del count_s2[s2[i - len_s1]]

          if count_s1 == count_s2:
              return True

      return False
  ```
