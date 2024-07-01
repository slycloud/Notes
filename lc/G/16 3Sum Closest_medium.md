## 16. 3Sum Closest

**medium**

Given an integer array nums of length n and an integer target, find three integers in nums such that the sum is closest to target.

Return the sum of the three integers.

You may assume that each input would have exactly one solution.

 

Example 1:

Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
Example 2:

Input: nums = [0,0,0], target = 1
Output: 0
Explanation: The sum that is closest to the target is 0. (0 + 0 + 0 = 0).
 

Constraints:

3 <= nums.length <= 500
-1000 <= nums[i] <= 1000
-104 <= target <= 104

### Solution

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        
        int ans = 0;
        int diff = Integer.MAX_VALUE;
        
        // -4, -1, 1, 2
        for (int i = 0; i + 2 < nums.length; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            for (int j = i + 1, k = nums.length - 1; j < k;) {
                int sum = nums[i] + nums[j] + nums[k];
                if (sum == target) {
                    return target;
                } else {
                    int newDiff = Math.abs(target - sum);
                    if (newDiff < diff) {
                        ans = sum;
                        diff = newDiff;
                    }
                    
                    if (sum < target) {
                        j++;
                    } else {
                        k--;
                    }
                }
            }
        }
        
        return ans;
    }
}
```
