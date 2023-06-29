# 目录
- [目录](#目录)
- [5. 最长回文子串](#5-最长回文子串)

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