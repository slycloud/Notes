## 4. Median of Two Sorted Arrays

**hard**

Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.

The overall run time complexity should be O(log (m+n)).

 

Example 1:

Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.
Example 2:

Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.
 

Constraints:

nums1.length == m
nums2.length == n
0 <= m <= 1000
0 <= n <= 1000
1 <= m + n <= 2000
-106 <= nums1[i], nums2[i] <= 106

### Solution

```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int N1 = nums1.length, N2 = nums2.length;
        if ((N1 + N2) % 2 == 0) {
            return (find((N1 + N2) / 2, nums1, 0, N1 - 1, nums2, 0, N2 - 1) + find((N1 + N2) / 2 + 1, nums1, 0, N1 - 1, nums2, 0, N2 - 1)) / 2.0;
        } else {
            return find((N1 + N2) / 2 + 1, nums1, 0, N1 - 1, nums2, 0, N2 - 1);
        }
    }
    
    // kth smallest number (1-based)
    private int find(int k, int[] nums1, int l1, int r1, int[] nums2, int l2, int r2) {
        if (r1 < l1) {
            return nums2[l2 + k - 1];
        }
        if (r2 < l2) {
            return nums1[l1 + k - 1];
        }
        
        // swap if nums1[l1] < nums2[l2] for simplicity
        if (nums1[l1] > nums2[l2]) {
            int[] tmp = nums1;
            nums1 = nums2;
            nums2 = tmp;
            int tmpValue = l1;
            l1 = l2;
            l2 = tmpValue;
            tmpValue = r1;
            r1 = r2;
            r2 = tmpValue;
        }
        
        if (nums2[l2] >= nums1[r1]) {
            if (k <= r1 - l1 + 1) {
                return nums1[l1 + k - 1];
            } else {
                k -= r1 - l1 + 1;
                return nums2[l2 + k - 1];
            }
        }
        
        int idx2 = l2 + (r2 - l2) / 2;
        int idx1 = findValue(nums1, l1, r1, nums2[idx2]);
        int count = idx2 - l2 + 1 + idx1 - l1 + 1;
        if (count == k) {
            return Math.max(nums1[idx1], nums2[idx2]);
        } else if (count < k) {
            return find(k - count, nums1, idx1 + 1, r1, nums2, idx2 + 1, r2);
        } else {
            return find(k, nums1, l1, idx1, nums2, l2, idx2);
        }
    }
    
    // find the last index whose value <= target
    private int findValue(int[] nums, int l, int r, int target) {
        r = r + 1;
        // find first num > target
        while (l < r) {
            int mid = l + (r - l) / 2;
            if (nums[mid] <= target) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        
        return l - 1;
    }
}
```
