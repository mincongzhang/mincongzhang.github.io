http://www.lintcode.com/en/tag/forward-backward-traversal/

### Product of Array Exclude Itself
Given an integers array A. Define B[i] = A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1], calculate B WITHOUT divide operation.

```
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        int size = nums.size();
        std::vector<long long> result(size,0);
        if(nums.empty()) return result;
        
        //Get products of left and right 
        std::vector<long long> left(size,1), right(size,1);
        for(int it(1); it < size; ++it){
            left[it] = left[it-1] * nums[it-1];
            
            int right_it = size-it-1; 
            right[right_it] = right[right_it+1] * nums[right_it+1];
        }
        
        //get result
        for(int it(0); it < size; ++it){
            result[it] = left[it] * right[it];
        }
        
        return result;
    }
};
```

```
//In Place Solution
class Solution {
public:
    /**
     * @param A: Given an integers array A
     * @return: A long long array B and B[i]= A[0] * ... * A[i-1] * A[i+1] * ... * A[n-1]
     */
    vector<long long> productExcludeItself(vector<int> &nums) {
        int size = nums.size();
        std::vector<long long> result(size,1);
        if(nums.size()<=1) return result;
        
        //Get products of right
        for(int it(size-1-1); it >=0 ; --it){
            result[it] *= result[it+1] * nums[it+1];
        }
        
        //get result
        long long temp(1);
        for(int it(0); it < size; ++it){
            result[it] *= temp;
            temp *= nums[it];
        }
        
        return result;
    }
};

```