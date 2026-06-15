## Baidyanath's DSA notes

**Problem:** Given an array, it might be an rotated version (clock-wise) of the original array.
*Check the orignial array was sorted in ascending order or not*
https://leetcode.com/problems/check-if-array-is-sorted-and-rotated/

Example: 4 5 1 2 3 # true

**Approach**: Break the array into two parts. First iterate over array get the second part index by using logic like n[i] > n[i + 1] (first-occurance), and check for both parts if they are sorted.

*other checks* - 
- before checking both parts check if no second part found, the array is not rotated and sorted
- after checking both parts at-last check if starting of array is less the last element, then this means original array is not sorted.

```cpp
bool check(vector<int>& nums) {
        unsigned int size = nums.size(), partBStart = 0;

        for (unsigned int i = 0; i < size - 1; i++) {
            if (nums[i] > nums[i + 1]) {
                partBStart = i + 1;
                break;
            }
        }

        if (partBStart == 0) return true; // not rotated and sorted

        // check part one
        for (unsigned int i = 0; i < partBStart - 1; i++) {
            if (nums[i] > nums[i + 1]) {
               return false;
            }
        }

        // check part 2
        for (unsigned int i = partBStart; i < size - 1; i++) {
            if (nums[i] > nums[i + 1]) {
               return false;
            }
        }

        // check if both parts together sorted
        if (partBStart >= 1 && nums[0] < nums[size - 1])
            return false;

        return true;
    }
```
---
**Problem** Remove duplicate elements from the array
Example: I/P [1,1,2,2,2,3,3]
O/P [1,2,3]

**Approach** Declare a set, push element. You got the unique elements
T.C = O(N log(N)) [inserting into set]
S.C = O(N)

**Optimal Approach** 
Two pointer approach
```cpp
int removeDuplicates(vector<int>& nums) {
    // Two pointer approach
    unsigned int i = 0, size = nums.size();
    for (unsigned int j = 1; j < size; j++) {
        // this is the go head pointer that will check 1 by 1
        // now check unique and place next to the i and update +1
        if (nums[i] != nums[j])
            nums[++i] = nums[j]; 
    }
    return i + 1;
}
```
T.C = O (N), S.C = O (1)

---
**Problem** Move 0s to the end of the array.
Example: I/P - [1, 0, 2, 3, 2, 0, 0, 4, 5, 1]
O/P - [1, 2, 3, 2, 4, 5, 1, 0, 0, 0]

**Approach** Pick all non-zero elements store it in another temporary array and modify array based on new array and fill last remaining with zeros.

T.C = O (N), S.C = O (N)

**Optimal Approach** 

Use Two pointer approach
```cpp
void moveZeroes(vector<int>& nums) {
    unsigned int size = nums.size(), i = -1;
    
    if (size <= 1) return;

    // make starting elements non 0
    for (unsigned int j = 0; j < size; j++) {
        if (nums[j] != 0)
            nums[++i] = nums[j];
    }

    // once that, from current i to end should be 0
    for (unsigned int j = i + 1; j < size; j++)
        nums[j] = 0;
}
```
T.C = O (N), S.C = O (1)
---
**Problem:** Longest subarray with given sum k. There are Two Types
- Only positive array:
- integer both positive and negative number in array

    E.g: I/P nums = [10, 5, 2, 7, 1, 9], k = 15
    O/P: 4 # as sub array is (5, 2, 7, 1)

https://www.geeksforgeeks.org/problems/longest-sub-array-with-sum-k0809/1

**Approach:** Brute force, find sum from each element that is equal to K. Update max by the sum of each element.

*Improvement*: Prefix sum hash
```cpp
int longestSubarray(vector<int>& arr, int k) {
    unordered_map<int, int> prefixSum;
    int size = arr.size(), s = 0, r, maxl = 0;
    
    for (int i = 0; i < size; i++) {
        s += arr[i];
        r = s - k;
        if (r == 0) {
            maxl = max(maxl, i + 1);
            // check if not present then insert
            if (prefixSum.find(s) == prefixSum.end())
                prefixSum[s] = i;
                continue;
        }
        auto ps = prefixSum.find(r);
        if (ps != prefixSum.end()) {
            maxl = max(maxl, i - ps->second);
        }
        // check if prefix sum is not present then insert
        if (prefixSum.find(s) == prefixSum.end())
                prefixSum[s] = i;
        
    }
    return maxl;
}
```
T.C = O (N), in worste case of hash-maps O (N log N),
S.C = O (N)

*Optimal*: Two pointer (only works for positive array)
```cpp
int longestSubarray(vector<int>& arr, int k) {
    // code here
    int i = 0, j = 0, s = 0, size = arr.size(), maxx = 0;
    
    while(i <  size && j < size) {
        if (arr[j] < k && s < k)
            s += arr[j++];
        // when sum is equal update max and start pointer i & substrack a[i] from sum
        if (s == k) {
            maxx = max(maxx, j - i);
            s -= arr[i++];
        } else if (s > k) {
            // just update the starting pointer i & substrack a[i] from sum
            s -= arr[i++];
        }
    }
    return maxx;
}
```
T.C = O (N), S.C = O (1)

**Problem**: In given array find two elements whose sum equal to target.

**Approach**: Brute force si N^2 approach

Better is using hash-map. keep element in hash map.
T.C = O (N log N), S.C = O (N)

In Optimal solution we can save space by sorting the array and then using two pointer solution

---
**Problem:** Sort Colors: Sort an array containing 3 numbers `0s`, `1s` and `2s`. Such that all the `0s` are at the start, `1s` at the middle and `2s` at that last.

Exmaple:
I/P: [2,0,2,1,1,0], O/P: [0,0,1,1,2,2]

**Approach:** Sort them using algo lib or quick or merge sort.

T.C= O (N log N), S.C = O (1)

*Optimal Approach:* Use three pointers start, mid and high.
start = 0, mid = 0 and last = last_element
```cpp
void sortColors(vector<int>& nums) {
    int start = 0, mid = 0, last = nums.size() - 1;
    
    while (mid <= last) {
        // if mid is 0 then 0 must go to start
        if (nums[mid] == 0) 
            swap(nums[mid++], nums[start++]);
        // if mid is 1 then it is in correct place
        else if (nums[mid] == 1) mid++;
        // if mid is 2 then it should go to last, no need to increase mid
        else if (nums[mid] == 2)
            swap(nums[mid], nums[last--]);
    }
}
```
T.C = O (N), S.C = O (1)

---
**Problem:** Given an array, find the subarray with maximum sum.

Example:

I/P: [2, 3, 5, -2, 7, -4], O/P: 15 # add from index 0 to 4

**Approach:** Brute force,
Two loops with one nested, find sum of each sub-array, and update max.
On each increament of out loop. Reduce the sum by a[i].

T.C: O (N ^ 2), S.C: O (1)

*Optimal Approach:* Kadane's algorithm  
```cpp
int maxSubArray(vector<int>& nums) {
    int n = nums.size(), maxx = INT_MIN, sum = 0;

    for (int i = 0; i < n; i++) {
        sum += nums[i];
        maxx = max(maxx, sum);

        if (sum < 0) sum = 0;
    }
    return maxx;
}
```
T.C: O (N), S.P: O (1) 

*Version 2*: Print the sub-array with max sum. 
https://www.geeksforgeeks.org/problems/maximum-sub-array5443/1

```cpp
vector<int> findSubarray(vector<int>& arr) {
    // code here
    vector<int> result;
    int size = arr.size(), maxx = INT_MIN, sum = 0, 
        start = 0, ansStart = 0, ansEnd = 0;
    bool allNeg = true;
    
    for (unsigned int i = 0; i < size; i++) {
        // check update allNeg
        if (arr[i] >= 0) allNeg = false;
        
        if (sum == 0) start = i;
        
        sum += arr[i];
        if (sum > maxx) {
            maxx = sum;
            ansStart = start;
            ansEnd = i;
        }
        
        if (sum < 0) {
            sum = 0;
        }
    }
    
    for (unsigned int i = ansStart; i <= ansEnd; i++){
        result.push_back(arr[i]);
    }
    
    if (allNeg) {
        return {-1};
    }
    return result;
}
```

----

**Problem** Stock Buy and Sell.
Given array of stock price in each day. Find the maximum profit.  
Example:  
I/P: [7, 1, 5, 3, 6, 4]
O/P: 5 # buy on day 2 (index 1) and sell on day 5 (index 4)

*Approach*: 
- Brute force: Nested loop with complexity N^2.
- Better: Sort the array and substract first and last element, with complexity O (n log n)
- Optiomal:  
```cpp
int maxProfit(vector<int>& prices) {
    int profit = 0, minCost = INT_MAX;

    for (int cost: prices) {
        if (cost < minCost) {
                minCost = cost;
                continue;
        }

        profit = max(profit, cost - minCost);
    }

    return profit;
}
```
Time Complexity:  O (N)

---

**Problem** Next Permutation  
I/P: [1, 3, 2]; O/P: [2, 1, 3]
For ascending order perputation of [1, 2, 3] -> [1, 3, 2] -> [2, 1, 3] -> [2, 3, 1] -> [3, 1, 2] -> [3, 2, 1]  
so [1, 3, 2] next permutation is [2, 1, 3]

*Approach:*  
Brute Force: Get all the permutation in sorted order and linear search the input and return the next element  

Better Approach: For C++ STL use next_permutation function

Optimal Approach:  
- First find the breakPoint index 
    - breakPoint index is found when a[i] > a[i + 1]
- if breakPoint index is not found then we are at last  
then we are at last permutation, then just reverse
- else, swap the breakPoint element with just near larger element from the subarray after breakPoint index
- reverse the subarray after breakPoint index

```cpp
void nextPermutation(vector<int>& nums) {
    unsigned int size = nums.size();
    int breakPoint = -1;

    // Get the breakpoint index
    for (int i = size - 2; i >= 0; i--) {
        if (nums[i] < nums[i + 1]) {
            breakPoint = i;
            break;
        }
    }

    // if breakPoint not found then it is last permuation, and 
    // need to show the first one reversing
    if (breakPoint == -1) {
        reverse(nums, 0, size - 1);
        return;
    }

    // find the number greater than breakPoint element in subarray after breakpoint and swap
    for (int i = size - 1; i > breakPoint; i--) {
        if (nums[breakPoint] < nums[i]) {
            swap(nums[breakPoint],  nums[i]);
            break;
        }
    }
    // after swapping just reverse the subarray after breakpoint
    reverse(nums, breakPoint + 1, size - 1);
}
```

T.C = O (N), S.C = O (1)

**Problem** Longest Consecutive sequence in array  
Input: nums = [100,4,200,1,3,2]  
Output: 4  
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.

*Approach*: 
- Better 
    - Sort the array
    - Loop through array and get max count of consecutive  
   
   Time Complexity: O (N log N)
- Optimal Approach: Uses unordered set DS.
The optimal approach is only optimal when there is no collision of buckets in unordered_set.  
Taking the advantage of set DS find() and insert() as both the methods have avg O (1) time complexity

```cpp
int longestConsecutive(vector<int>& nums) {
    unsigned int longest = 1;
    unordered_set<int> st;
    if (nums.size() <= 0) return 0;

    // inserting all to the set
    for (auto& it: nums) {
        st.insert(it);
    }

    for (auto& it: st) {
        // check if the element is first element in sequece, as the first one will have no x - 1 element
        if (st.find(it - 1) == st.end()) {
            unsigned int cnt = 1;
            int x = it;
            // keep finding and looping through the next element in the set
            while (st.find(x + 1) != st.end()) {
                cnt++;
                x++;
            }
            longest = max(longest, cnt);
        }
    }


    return longest;
}
```
**Problem** Pascal Triangle: https://leetcode.com/problems/pascals-triangle/description

My Solution to generate the triangle:
```cpp
class Solution {
    vector<int> genRows(vector<int>& prevRow, int rowNo) {
        vector<int> row = {1};
        for (int i = 1; i < rowNo; i++) {
            row.push_back(prevRow[i - 1] + prevRow[i]);
        }
        row.push_back(1);
        return row;
    }
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> result;
        result.push_back({1});

        if (numRows == 1) return result;

        result.push_back({1, 1});

        if (numRows == 2) return result;

        for (int i = 2; i < numRows; i++) {
            result.push_back(genRows(result[i - 1], i));
        }

        return result;
    }
};
```

But not efficient for printing the nth row or particular element

To print a perticular element from ith row and jth column. (Zero indexed)

use formula  
$$
{}^nC_r = \frac{n!}{r!(n-r)!}
$$
To get i<sup>th</sup> row and j<sup>th</sup> column element use 
$$
{}^iC_j = \frac{i!}{j!(i-j)!}
$$

However while printing the entire row this generating each element with nCr is costly due to looping.

*Approach*: Generate the next element based on previous element.
Explaination:
suppose row is 5<sup>th</sup>  
Col 0: <sup>5</sup>C<sub>0</sub> = 5!/5! = 1  
Col 1: 5!/4! = (5x4x3x2x1)/(4x3x2x1) = 1 x 5/1  
Col 2: 5!/(2!.3!) = (5x4x3x2x1)/(2x1)(3x2x1) = [1 x 5/1] x 4/2  
.  
.  
So elem = prevElem x [(row - i) / i]

```cpp
class Solution {
    vector<int> genRows(int rowNo) {
        vector<int> row = {1};
        int ans = 1;
        for (int i = 1; i < rowNo; i++) {
            ans *= (rowNo - i + 1);
            ans /= i;
            row.push_back(ans);
        }
        row.push_back(1);
        return row;
    }
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> result;
        result.push_back({1});
        if (numRows == 1) {
            return result;
        }
        
        for (int i = 1; i < numRows; i++) {
            result.push_back(genRows(i));
        }
        return result;
    }
};
```
**Problem:** Majority elements, finding elements whose frequency > N/3.

I/P = [1, 1, 3, 3, 1, 2, 2, 2]  
O/P = [1, 2]

*Observation:* Output have minimum 0 elements and max 2 elements

*Approach:*  
- Brute force: Count each element check if greater than n/3 then push to result
- Better: 
    - Sort the array and check and count element wise
    - use map to kep count record and check count and return the result
- Optimal: Boyer's Algo
Just instead of keeping 1 counter and 1 majElem. 2 counters and 2 Maj Elems will be maintained.
```cpp
vector<int> majorityElement(vector<int>& nums) {
    vector<int> result;
    int mElem1 = INT_MIN, mElem2 = INT_MIN, cnt1 = 0, cnt2 = 0, size = nums.size();

    for (int i = 0; i < size; i++) {
        // the second condition is for not keeping track of same elements in both counts
        if (cnt1 == 0 && mElem2 != nums[i]) {
            mElem1 = nums[i];
            cnt1 = 1;
        } else if (cnt2 == 0 && mElem1 != nums[i]) {
            mElem2 = nums[i];
            cnt2 = 1;
        } else if (mElem1 == nums[i]) {
            cnt1++;
        } else if (mElem2 == nums[i]) {
            cnt2++;
        } else { cnt1--; cnt2--; }
    }

    // Now checking if both the obtained elements frequency > n/3
    cnt1 = 0; cnt2 = 0;

    for (int i = 0; i < size; i++) {
        if (nums[i] == mElem1) cnt1++;
        if (nums[i] == mElem2) cnt2++;
    }

    if (cnt1 > size / 3) result.push_back(mElem1);
    if (cnt2 > size / 3) result.push_back(mElem2);

    return result;
    
}
```
T.C = O (n), S.C = O (1)

