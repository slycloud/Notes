## 17. Letter Combinations of a Phone Number

**medium**

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

Example 1:

Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
Example 2:

Input: digits = ""
Output: []
Example 3:

Input: digits = "2"
Output: ["a","b","c"]
 

Constraints:

0 <= digits.length <= 4
digits[i] is a digit in the range ['2', '9'].


### Solution

```java
class Solution {
    Map<Integer, char[]> map = Map.ofEntries(
        Map.entry(2, new char[] {'a', 'b', 'c'}),
        Map.entry(3, new char[] {'d', 'e', 'f'}),
        Map.entry(4, new char[] {'g', 'h', 'i'}),
        Map.entry(5, new char[] {'j', 'k', 'l'}),
        Map.entry(6, new char[] {'m', 'n', 'o'}),
        Map.entry(7, new char[] {'p', 'q', 'r', 's'}),
        Map.entry(8, new char[] {'t', 'u', 'v'}),
        Map.entry(9, new char[] {'w', 'x', 'y', 'z'})
    );
    
    public List<String> letterCombinations(String digits) {
        if (digits.isEmpty()) {
            return Arrays.asList();
        }
        
        int n = digits.charAt(0) - '0';
        List<String> subCombines = letterCombinations(digits.substring(1));
        List<String> ans = new LinkedList<>();
        for (Character c : map.get(n)) {
            if (subCombines.isEmpty()) {
                ans.add(c + "");
            } else {
                for (String sub : subCombines) {
                    ans.add(c + sub);
                }
            }
        }
        return ans;
    }
}
```
