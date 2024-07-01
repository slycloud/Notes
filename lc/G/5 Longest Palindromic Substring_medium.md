## 5. Longest Palindromic Substring

**medium**

Given a string s, return the longest 
palindromic
 
substring
 in s.


Example 1:

Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
Example 2:

Input: s = "cbbd"
Output: "bb"
 

Constraints:

1 <= s.length <= 1000
s consist of only digits and English letters.


### Solution

```java
class Solution {
    public String longestPalindrome(String s) {
        boolean[] dp = new boolean[s.length()];
        
        String ans = "";
        for (int i = s.length() - 1; i >= 0; i--) {
            for (int j = s.length() - 1; j >= i; j--) {
                dp[j] = s.charAt(i) == s.charAt(j) && (i + 1 >= j - 1 || dp[j - 1] == true);
                if (dp[j] && ans.length() < j - i + 1) ans = s.substring(i, j + 1);
            }
        }
        
        return ans;
    }
}
```
