### Single number
http://www.lintcode.com/en/problem/single-number/
```
/*
"^": XOR
0101^1111 = 1010 
*/

class Solution {
public:
    int singleNumber(vector<int> &A) {
        if(A.empty()) return 0;
        
        int ret;
        for(vector<int>::const_iterator it = A.begin(); it != A.end(); ++it)
            ret ^= *it;
            
        return ret;
    }
};
```

### Single number 2
http://www.lintcode.com/en/problem/single-number-ii/  
http://www.jiuzhang.com/solutions/single-number-ii/  
```
class Solution {
public:
    int singleNumberII(vector<int> &A) {
        if(A.empty()) return 0;

        std::map<int,int> record;
        std::map<int,int>::iterator record_it;
        int ret;
        for(vector<int>::iterator it = A.begin(); it != A.end(); ++it){
            record_it = record.find(*it);
            if( record_it == record.end() ){ //not found
                ret = *it;
                record.insert(std::pair<int,int>(*it,1));
                continue;
            }
             
            //found
            record_it->second++;
        }

        return ret;
    }
};
```

```
class Solution {
public:
    int singleNumberII(vector<int> &A) {
        if(A.empty()) return 0;

        int max(0),min(0);
        for(vector<int>::iterator it = A.begin(); it != A.end(); ++it){
            if(*it > max) max = *it;
            if(*it < min) min = *it;
        }


        std::vector<bool> recorder(max-min+1,false);
        int ret,pos;
        for(vector<int>::iterator it = A.begin(); it != A.end(); ++it){
            pos = *it - min;
            if(!recorder[pos]){ 
                ret = *it;
                recorder[pos] = true;
            }
        }

        return ret;
    }
};
```
//above solution wrong: 
//[1 2 2 2]
//ret will be 2


```
//BIt vector 
//http://blog.csdn.net/imabluefish/article/details/38822061
```

```
//count bit 
//http://fisherlei.blogspot.co.uk/2013/11/leetcode-single-number-ii-solution.html
//除了一个数字以外，其他的都出现了3次，如果我们把那个特殊的数剔除，并把剩下的数于每一位来加和的话，每一位上1出现的次数必然都是3的倍数。所以，解法就在这里，将每一位数字分解到32个bit上，统计每一个bit上1出现的次数。最后对于每一个bit上1出现的个数对3取模，剩下的就是结果。

 1     int singleNumber(int A[], int n) {
 2         vector<int> bit(32,0);
 3 
 4         for(int i =0; i< n; ++i)
 5         {
 6             int k=1;
 7             for(int j =0; j<32; ++j)
 8             {
 9                 int rotated;
10                 if((rotated = A[i]>>j) == 0) break;
11                 bit[j] += rotated & k;
12             }
13         }
14         
15         int target=0;
16         for(int i =0; i<32; ++i)
17         {
18             target += (bit[i]%3 <<i);
19         }
20         return target;
21     }
```

```
//Best answer

int singleNumber(int A[], int n) {
        int x = 0x0, y = 0x0;
        for (int i = 0; i < n; i++) {
            x ^= ~ y & A[i];
            y ^= ~ x & A[i];
        }
        return x;
}﻿
```
