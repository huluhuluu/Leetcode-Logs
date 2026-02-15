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

## (02.16) 
### 1. 题目
[]()：
### 2. 思路
### 3. 代码
```cpp

```
```python

```
### 4. 学习