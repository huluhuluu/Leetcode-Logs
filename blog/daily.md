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

## (02.20) 特殊的二进制字符串
### 1. 题目
[特殊的二进制字符串](https://leetcode.cn/problems/special-binary-string/description/?envType=daily-question&envId=2026-02-20)：特殊的二进制字符串 是具有以下两个性质的二进制序列：

0 的数量与 1 的数量相等。
二进制序列的每一个前缀码中 1 的数量要大于等于 0 的数量。
给定一个特殊的二进制字符串 s。

一次移动操作包括选择字符串 s 中的两个连续的、非空的、特殊子串，并交换它们。两个字符串是连续的，如果第一个字符串的最后一个字符与第二个字符串的第一个字符的索引相差正好为 1。

返回在字符串上应用任意次操作后可能得到的字典序最大的字符串。
### 2. 思路
模拟，先找出所有特殊子串，然后两两比较，如果前一个子串字典序小于后一个子串字典序，并且前一个子串不是后一个子串的前缀，则交换两个子串。重复这个过程直到没有可以交换的子串为止。

会不会出现交换后的子串不是特殊子串的情况？不会，因为交换的两个子串都是特殊子串，交换后仍然是特殊子串，可以继续交换。
### 3. 代码
```cpp
class Solution {
public:
    string makeLargestSpecial(string s) {
        int n = s.length();
        auto check = [&n, &s](){
            vector<pair<int, int> > pos;
            for(int i=0;i<n;i++){
                for(int l=2;l+i<=n;l++){
                    int cnt0=0, cnt1=0, idx = 0, flag = true;
                    while(idx<l){
                        cnt0 += s[i+idx]=='0', cnt1 += s[i+idx]=='1', idx++;
                        if(cnt0>cnt1){flag=false; break;}
                    }
                    if(flag && cnt0 == cnt1) pos.push_back(std::make_pair(i,l));
                }
            }
            return pos;
        };
        auto getStr = [](string& s, int p, int l){
            return s.substr(p, l);
        };
        while(1){
            bool flag = true;
            auto l = check();
        
            bool jumpflag = false;
            for(int i=0;i<l.size();i++){
                for(int j=i+1;j<l.size();j++){
                    int b1 = l[i].first, l1 = l[i].second;
                    int b2 = l[j].first, l2 = l[j].second;
                    if(b1 + l1 != b2) continue;
                    string tmp_s1 = getStr(s, b1, l1);
                    string tmp_s2 = getStr(s, b2, l2);
                    if(tmp_s1 < tmp_s2 && tmp_s1 != getStr(tmp_s2, 0, l1)){
                        tmp_s1 = tmp_s2 + tmp_s1;
                        for(int j=0;j<tmp_s1.length();j++){
                            s[b1+j] = tmp_s1[j]; 
                        }
                        flag = false;
                        jumpflag = true;
                        break;
                    }
                }
                if(jumpflag) break;
                
            }
            if(flag) break;
        }
        return s;
    }
};
```
```python
class Solution:
    def makeLargestSpecial(self, s: str) -> str:
        n = len(s)
        def checkSub(s, b, l):
            cnt0, cnt1, flag = 0, 0, True
            for i in range(l):
                cnt0, cnt1 = cnt0 + (s[b+i]=='0'), cnt1 + (s[b+i]=='1')
                if cnt0 > cnt1:
                    flag = False
                    break
            return flag and (cnt0 == cnt1)

        def findSub(s):
            pos, n = [[] for c in s], len(s)
            for i in range(n):
                for l in range(2, n+1):
                    if i+l > n:
                        break
                    if checkSub(s, i, l):
                        pos[i].append(l)
            return pos
        while 1:
            bflag, jumpflag, pos = True, False, findSub(s)
            for i in range(n):
                if len(pos[i]) == 0:
                    continue
                for il in pos[i]:
                    if i + il >= n or len(pos[i + il]) == 0:
                        continue
                    j = i + il
                    for jl in pos[i + il]:
                        if s[i:i+il] < s[j:j+jl] and s[j:j+jl][:il] != s[i:i+il]:
                            # swap
                            # s[i:i+il+jl] = s[j:jl] + s[i:i+il]
                            s = s[:i] + s[j:j+jl] + s[i:i+il] + s[j+jl:]
                            jumpflag, bflag = True, False
                            break
                    if jumpflag:
                        break
                if jumpflag:
                    break

            if bflag:
                break
        return s
```
### 4. 学习
python切片 如果是不可变对象如字符串，切片会返回一个新的对象；如果是可变对象如列表，切片会返回一个新的对象，但其中的元素仍然是原来对象中的元素的引用。

题解大概是分成大的子串，子串内部进行排序，最后把子串进行排序。

## (02.21) 二进制表示中质数个计算置位
### 1. 题目
[二进制表示中质数个计算置位](https://leetcode.cn/problems/prime-number-of-set-bits-in-binary-representation/description/?envType=daily-question&envId=2026-02-21)：给你两个整数 left 和 right ，在闭区间 [left, right] 范围内，统计并返回 计算置位位数为质数 的整数个数。

计算置位位数 就是二进制表示中 1 的个数。

例如， 21 的二进制表示 10101 有 3 个计算置位。
### 2. 思路
打表质数(这里位数最多32位)，然后遍历区间统计，用位运算只数1的个数。
### 3. 代码
```cpp
class Solution {
public:
    int countPrimeSetBits(int left, int right) {
        vector<int> prime(33, 0);
        prime[2] = prime[3] = prime[5] = prime[7] = prime[11] = prime[13] = prime[17] = prime[19] = prime[23] = prime[29] = prime[32] = 1;
        auto cntOne = [](int num){
            int res = 0;
            while(num){
                res += 1, num = num & (num - 1);
            }
            return res;
        };
        int res = 0;
        while(left<=right){
            res += prime[cntOne(left++)];
        }
        return res;
    }
};
```
### 4. 学习
题解把质数判断从数组变成数位mask

## (02.22) 二进制间距
### 1. 题目
[二进制间距](https://leetcode.cn/problems/binary-gap/description/?envType=daily-question&envId=2026-02-22)：给定一个正整数 n，找到并返回 n 的二进制表示中两个 相邻 1 之间的 最长距离 。如果不存在两个相邻的 1，返回 0 。

如果只有 0 将两个 1 分隔开（可能不存在 0 ），则认为这两个 1 彼此 相邻 。两个 1 之间的距离是它们的二进制表示中位置的绝对差。例如，"1001" 中的两个 1 的距离为 3 。
### 2. 思路
直接模拟，记录上一个1的位置，计算当前1与上一个1的距离，更新最大距离。
### 3. 代码
```cpp
class Solution {
public:
    int binaryGap(int n) {
        int res = 0, cnt = 0;
        while(n){
            if(n&1) res = max(res, cnt), cnt = 1;
            else if(cnt>0) cnt++;
            n>>=1;
        }
        return res;
    }
};
```
```python
class Solution:
    def binaryGap(self, n: int) -> int:
        cnt, res = 0, 0
        while n:
            if n&1==1:
                res, cnt = max(res, cnt), 1
            elif cnt>0:
                cnt += 1
            n >>= 1
        return res
```

## (02.23) 检查一个字符串是否包含所有长度为 K 的二进制子串
### 1. 题目
[检查一个字符串是否包含所有长度为 K 的二进制子串](https://leetcode.cn/problems/check-if-a-string-contains-all-binary-codes-of-size-k/description/?envType=daily-question&envId=2026-02-22)：给你一个二进制字符串 s 和一个整数 k 。如果所有长度为 k 的二进制字符串都是 s 的子串，请返回 true ，否则请返回 false 。

### 2. 思路
滑动窗口 + 哈希，判断结果只需要看哈希表的长度是否是2^k。

### 3. 代码
```cpp
class Solution {
public:
    bool hasAllCodes(string s, int k) {
        map<string, int> mp;
        string tmp_s = s.substr(0,k);
        mp[tmp_s] = 1;
        for(int i=k;i<s.length();i++){
            // 滑动窗口
            tmp_s = tmp_s.substr(1) + s[i];
            mp[tmp_s] = 1;
        }
        return mp.size() == pow(2,k);
    }
};
```
```python
class Solution:
    def hasAllCodes(self, s: str, k: int) -> bool:
        tmp_s, mp = s[0:k], {s[0:k]:1}
        for i in range(k, len(s)):
            tmp_s, mp[tmp_s] = tmp_s[1:] + s[i], 1
        return len(mp) == 2**k
```
### 4. 学习
数串长度短，可以直接用字符串哈希。

## (02.24) 
### 1. 题目
[]()：
### 2. 思路
### 3. 代码
```cpp

```
```python

```
### 4. 学习