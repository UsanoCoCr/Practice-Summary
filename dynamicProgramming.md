# 目录
- [目录](#目录)
- [5. 最长回文子串](#5-最长回文子串)
- [72. 编辑距离](#72-编辑距离)

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


 