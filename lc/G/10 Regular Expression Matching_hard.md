## 10. Regular Expression Matching

**hard**

Given an input string s and a pattern p, implement regular expression matching with support for '.' and '*' where:

'.' Matches any single character.​​​​
'*' Matches zero or more of the preceding element.
The matching should cover the entire input string (not partial).

 

Example 1:

Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
Example 2:

Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
Example 3:

Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
 

Constraints:

1 <= s.length <= 20
1 <= p.length <= 20
s contains only lowercase English letters.
p contains only lowercase English letters, '.', and '*'.
It is guaranteed for each appearance of the character '*', there will be a previous valid character to match.

### Solution

```java
class Solution {
    // always treat "x*" (x followed by *) as a group
    public boolean isMatch(String s, String p) {
        return isMatch(s, p, 0, 0);
    }
    
    private boolean isMatch(String s, String p, int ps, int pp) {
        while (pp < p.length()) {
            char pchar = p.charAt(pp);
            if (pp + 1 < p.length() && p.charAt(pp + 1) == '*') {
                while (!isMatch(s, p, ps, pp + 2)) {
                    if (ps < s.length() && isCharMatch(s.charAt(ps), pchar)) {
                        ps++;
                    } else {
                        return false;
                    }
                }
                return true;
            } else {
                if (ps < s.length() && isCharMatch(s.charAt(ps), pchar)) {
                    pp++;
                    ps++;
                    continue;
                } else {
                    return false;
                }
            }
        }
        return pp == p.length() && ps == s.length();
    }
    
    // t will only be 'a-z' or '.'
    private boolean isCharMatch(char c, char t) {
        if (t >= 'a' && t <= 'z') return c == t;
        else if (t == '.') return true;
        else return false;  // won't happen
    }
}
```
