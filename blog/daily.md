# Leetcode 每日一题
记录力扣每日一题
# 2026.02
## (02.15)二进制求和
### 1. 题目
[二进制求和](https://leetcode.cn/problems/add-binary/description/?envType=daily-question&envId=2026-02-15): 给你两个二进制字符串 a 和 b ，以二进制字符串的形式返回它们的和。

### 2. 思路
模拟二进制加法，从末尾位置开始往前模拟，例如
> 输入:a = "11", b = "1"
输出："100"

先加a和b的最低位结果是1+1=10 继续用结果加a和b的最高位就是10+10+00=100.

可以用双指针指向两个字符串末尾，再依次往前加值。
### 3. 代码
```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        int p1 = a.length()-1, p2 = b.length()-1, flag = 0;
        string ans = "";
        while(p1>=0 || p2>=0){ // 双指针
            int sum = flag;
            if(p1>=0) sum += a[p1];
            if(p2>=0) sum += b[p2];
            flag = (sum & 2)>>1; // 标志位记录进位
            ans = to_string(sum & 1) + ans;
            p1--, p2--;
        }
        if(flag) ans = "1" + ans;   // 处理最后进位
        if(ans == "") return "0";   // 特殊情况
        return ans;
    }
};
```
```python
class Solution:
    def addBinary(self, a: str, b: str) -> str:
        p1, p2, flag, res = len(a) - 1, len(b) - 1, 0, ""
        while p1 >= 0 or p2>=0:
            sum = flag
            if p1 >= 0:
                sum += int(a[p1])
            if p2 >= 0:
                sum += int(b[p2])
            flag, res = sum // 2, str(sum&1) + res
            p1, p2= p1-1, p2-1
        if flag:
            res = "1" + res
        if res == "":
            res = "0"
        return res
```
### 4. 学习
python自带大数处理，可以直接转为10进制加法再转为2进制；
python中```int(a,b)```表示把字符串a解释为b进制

## (02.16) 颠倒二进制位
### 1. 题目
[颠倒二进制位](https://leetcode.cn/problems/reverse-bits/description/?envType=daily-question&envId=2026-02-16)：颠倒给定的 32 位有符号整数的二进制位。

### 2. 思路
把结果逐步右移与上逐步左移的值最低位,注意这里不缺省前缀0,cpp里int右移32位是未定义行为.
### 3. 代码
```cpp
class Solution {
public:
    int reverseBits(int n) {
        int res = 0, bits = 0;
        while(n!=0){
            res = (res<<1) | (n&1);
            bits+=1, n>>=1;
        }
        return ((long) res<<(32-bits));
    }
};
```
```python
class Solution:
    def reverseBits(self, n: int) -> int:
        res, bits = 0, 0
        while n:
            res = (res<<1) | (n&1)
            n >>= 1
            bits += 1
        return res<<(32-bits)
```
### 4. 学习

## (02.17) 二进制手表
### 1. 题目
[二进制手表](https://leetcode.cn/problems/binary-watch/description/?envType=daily-question&envId=2026-02-17)：二进制手表顶部有 4 个 LED 代表 小时（0-11），底部的 6 个 LED 代表 分钟（0-59）。每个 LED 代表一个 0 或 1，最低位在右侧。

给你一个整数 turnedOn ，表示当前亮着的 LED 的数量，返回二进制手表可以表示的所有可能时间。你可以 按任意顺序 返回答案。

小时不会以零开头：

例如，"01:00" 是无效的时间，正确的写法应该是 "1:00" 。
分钟必须由两位数组成，可能会以零开头：

例如，"10:2" 是无效的时间，正确的写法应该是 "10:02" 。
### 2. 思路
#### 2.1 思路1
直接遍历所有可能时间点
#### 2.2 思路2
对小时和分钟的情况打表，然后分配亮灯LED数量给小时和分钟。
### 3. 代码
```cpp
class Solution {
public:
    vector<string> readBinaryWatch(int turnedOn) {
        auto countOne = [](int num){
            int cnt = 0;
            while(num){
                cnt+=(num&1);
                num>>=1;
            }
            return cnt;
        };
        // 打表小时和分钟的可能性
        vector<vector<string>> h(20);
        vector<vector<string>> m(60);
        for(int i=0;i<=11;i++) h[countOne(i)].push_back(to_string(i));
        for(int i=0;i<=59;i++){
            if(i<10){
                m[countOne(i)].push_back("0"+to_string(i));
            }
            else{
                m[countOne(i)].push_back(to_string(i));
            }
        }
        vector<string> ans;
        for(int i=0;i<=turnedOn;i++){
            int h_one = i, m_one = turnedOn - i;
            for(string& hstr: h[h_one]){
                for(string& mstr: m[m_one]){
                    ans.emplace_back(hstr+":"+mstr);
                }
            }
        }
        return ans;
    }
};
```
```python
class Solution:
    def readBinaryWatch(self, turnedOn: int) -> List[str]:
        h, m, ans = [[] for i in range(12)], [[] for i in range(60)], []
        def cntOne(num: int):
            res = 0
            while num>0:
                res, num = res + (num&1), num>>1
            return res
        
        for i in range(12):
            h[cntOne(i)].append(f"{i}")
        for i in range(60):
            m[cntOne(i)].append(f"{i}" if i>=10 else f"0{i}")
        
        for i in range(turnedOn + 1):
            hone, mone = i, turnedOn - i
            for hstr in h[hone]:
                for mstr in m[mone]:
                    ans.append(hstr + ":" + mstr)

        return ans
```
### 4. 学习
cpp中`popcount(x) `获得x的二进制表示中1的个数，python中`bin(x).count("1")`获得x的二进制表示中1的个数.

## (02.18) 交替位二进制数
### 1. 题目
[交替位二进制数](https://leetcode.cn/problems/binary-number-with-alternating-bits/description/?envType=daily-question&envId=2026-02-18)：给定一个正整数，检查它的二进制表示是否总是 0、1 交替出现：换句话说，就是二进制表示中相邻两位的数字永不相同。
### 2. 思路
#### 2.1 思路1
直接模拟，逐位检查相邻两位是否相同。
### 3. 代码
```cpp
class Solution {
public:
    bool hasAlternatingBits(int n) {
        bool flag = n&1;
        while(n>0){
            if(flag == (bool)(n&1)) n = n>>1, flag = !flag;
            else return false;
        }
        return true;
    }
};
```
```python
class Solution:
    def hasAlternatingBits(self, n: int) -> bool:
        flag = n&1
        while n:
            if n&1 == flag:
                n, flag = n>>1, not flag
            else:
                return False
        return True
```
### 4. 学习
这里cpp的比较要注意类型转换，不会自动转换为bool类型，python的比较会自动转换为bool类型。
可以直接位运算。

## (02.19) 计数二进制子串
### 1. 题目
[计数二进制子串](https://leetcode.cn/problems/count-binary-substrings/description/?envType=daily-question&envId=2026-02-19)：给定一个字符串 s，统计并返回具有相同数量 0 和 1 的非空（连续）子字符串的数量，并且这些子字符串中的所有 0 和所有 1 都是成组连续的。

重复出现（不同位置）的子串也要统计它们出现的次数。
### 2. 思路
单次扫描，统计连续0和连续1的数量，结果就是相邻两组0和1的数量较小值之和。
### 3. 代码
```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int flag = s[0] - '0', res = 0, cnt = 0, last_cnt = 0, idx = 0, l = s.length();
        while(idx<l){
            while(idx<l && s[idx] - '0'==flag) cnt++, idx++;
            res = res + min(cnt, last_cnt), last_cnt=cnt, cnt = 0, flag = 1-flag;
        }
        return res;

    }
};
```
```python
class Solution:
    def countBinarySubstrings(self, s: str) -> int:
        cnt, last_cnt, flag, res, n, idx = 0, 0, int(s[0]), 0, len(s), 0
        while idx < n:
            while idx < n and int(s[idx])==flag:
                idx, cnt = idx + 1, cnt + 1
            res, last_cnt, cnt, flag = res + min(cnt, last_cnt), cnt, 0, 1 - flag
        return res
```

## (02.20) 
### 1. 题目
[]()：
### 2. 思路
### 3. 代码
```cpp

```
```python

```
### 4. 学习