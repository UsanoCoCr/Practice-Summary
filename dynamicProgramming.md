# 目录
- [目录](#目录)
- [5. 最长回文子串](#5-最长回文子串)
- [72. 编辑距离](#72-编辑距离)
- [1218. 最长定差子序列](#1218-最长定差子序列)
- [300. 最长递增子序列（二分）](#300-最长递增子序列二分)

# 5. 最长回文子串
给你一个字符串 s，找到 s 中最长的回文子串。

如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

<details>
<summary>最长回文子串</summary>

```c++
string longestPalindrome(string s) {
    bool f[1001][1001]={0};//f[i][j]表示i到j是否是回文串
    int sStart = 0, sLength = 1;
    // start_state
    for(int i=0;i<s.size();i++) {
        f[i][i]=1;
        if(i != s.size()-1 && s[i] == s[i+1]){
            f[i][i+1]=1;
            if(sLength < 2){
                sStart = i;
                sLength = 2;
            }
        }
    }
    //
    for(int i=3;i<=s.size();i++){
        for(int j=0;j<=s.size()-i;j++){
            if(f[j+1][j+i-2] && s[j] == s[j+i-1]){
                f[j][j+i-1] = 1;
                if(sLength < i){
                    sStart = j;
                    sLength = i;
                }
            }
        }
    }
    return s.substr(sStart, sLength);
}
```
</details>

# 72. 编辑距离
给你两个单词 word1 和 word2， 请返回将 word1 转换成 word2 所使用的最少操作数  。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符

**要点：** 量化插入、删除、替换元素的操作，我的f设置是对的，但是还没有理解这三个操作的表现。
<details>
<summary>编辑距离</summary>

```c++
class Solution {
public:
    int f[501][501]={0};//f[i][j]表示word1到i，word2到j时的最短替换
    /* f[i-1][j-1]+1表示替换元素
    f[i-1][j]+1表示插入元素
    f[i][j-1]+1表示删除元素 */
    int minDistance(string word1, string word2) {
        f[0][0] = 0;
        for(int i=1;i<=word2.size();i++){
            f[0][i] = f[0][i-1] + 1;
        }
        for(int i=1;i<=word1.size();i++){
            f[i][0] = f[i-1][0] + 1;
        }
        for(int i=1;i<=word1.size();i++){
            for(int j=1;j<=word2.size();j++){
                if(word1[i-1] == word2[j-1]){
                    f[i][j] = f[i-1][j-1];
                }
                else{
                    f[i][j] = min(f[i-1][j], f[i][j-1])+1;
                    f[i][j] = min(f[i][j], f[i-1][j-1]+1);
                }
            }
        }
        return f[word1.size()][word2.size()];
    }
};
```
</details>

# 1218. 最长定差子序列
给你一个整数数组 arr 和一个整数 difference，请你找出并返回 arr 中最长等差子序列的长度，该子序列中相邻元素之间的差等于 difference 。

子序列 是指在不改变其余元素顺序的情况下，通过删除一些元素或不删除任何元素而从 arr 派生出来的序列。

**要点：** 如果使用传统的从前往后按位置找的dp，那么$10^5$的数据量显然会使$O(n^2)$的算法超时。所以我们需要找到一个$O(n)4的方法来更新dp数组。

<details>
<summary>最长定差子序列</summary>

```c++
class Solution {
public:
    int f[40001]={0};//f[i]表示以i结尾的最长等差子序列的长度
    int longestSubsequence(vector<int>& arr, int difference) {
        int ans = 0;
        for(int i: arr){
            f[i+20000] = f[i-difference+20000] + 1;
            ans = max(ans, f[i+20000]);
        }
        return ans;
    }
};
```
</details>
 
# 300. 最长递增子序列（二分）
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

**要点：** 本题的难点在于如何找到$O(nlogn)$的算法。我们可以使用一个数组来存储当前最长的递增子序列，然后使用二分查找来更新这个数组。

**理解：** 直接看文字确实不太好懂，加个例子就比较容易明白，比如序列是78912345，前三个遍历完以后tail是789，这时候遍历到1，就得把1放到合适的位置，于是在f二分查找1的位置，变成了189，再遍历到2成为129，然后是123直到12345

<details>
<summary>最长递增子序列</summary>

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> f;//f[i]表示长度为i的递增子序列的最小末尾元素
        f.push_back(nums[0]);
        for(int i=1;i<nums.size();i++){
            if(nums[i] > f.back()){
                f.push_back(nums[i]);
            }
            else{
                int l = 0, r = f.size()-1;
                while(l < r){
                    int mid = (l+r)/2;
                    if(f[mid] < nums[i]){
                        l = mid+1;
                    }
                    else{
                        r = mid;
                    }
                }
                f[l] = nums[i];
            }
        }
        return f.size();
    }
};
```
</details>
