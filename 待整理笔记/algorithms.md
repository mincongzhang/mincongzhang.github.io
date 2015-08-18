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
http://blog.csdn.net/imabluefish/article/details/38822061
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
//hash map
//bloom filter
```
