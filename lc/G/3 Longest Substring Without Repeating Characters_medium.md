## 3. Longest Substring Without Repeating Characters

**medium**

Given a string s, find the length of the longest 
substring without repeating characters.


Example 1:

Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
Example 2:

Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
Example 3:

Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
 

Constraints:

0 <= s.length <= 5 * 104
s consists of English letters, digits, symbols and spaces.
### Solution

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int ans = 0;
        
        Map<Character, Integer> map = new HashMap<>();
        int ps = 0, pe = 0;
        while (pe < s.length()) {
            char c = s.charAt(pe);
            int idx = map.getOrDefault(c, -1);
            if (idx < ps) {
                map.put(c, pe++);
            } else {
                ans = Math.max(ans, pe - ps);
                ps = idx + 1;
                map.put(c, pe++);
            }
        }
        // update ans
        ans = Math.max(ans, pe - ps);
        
        return ans;
    }
}
```

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int ans = 0;
        
        Map<Character, Integer> map = new HashMap<>();
        int ps = 0, pe = 0;
        while (pe < s.length()) {
            char c = s.charAt(pe);
            int idx = map.getOrDefault(c, -1);
            if (idx < ps) {  // note: can not be "idx == -1"
                map.put(c, pe++);
            } else {
                ans = Math.max(ans, pe - ps);
                
                // try to find any repeating characters continuously
                int p = pe;
                while (p + 1 < s.length() && c == s.charAt(p + 1)) {
                    p++;
                }
                if (pe == p) {
                    ps = map.get(c) + 1;
                    map.put(c, pe);
                } else {
                    ps = p;
                    map.put(c, p);
                }
                pe = p + 1;
            }
        }
        // update ans
        ans = Math.max(ans, pe - ps);
        
        return ans;
    }
}
```
