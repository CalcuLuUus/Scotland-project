# 区间dp

## P4302 [SCOI2003]字符串折叠

1. 通过向两边是否为相同字符判断是否可以合并的特征 + 长区间可以从两个短区间合并得知大概是个区间dp

2. 区间dp的转移有两种可能，对于$dp[l][r]$

   1. $dp[l][r] = min(dp[l][k] + dp[k+1][r], dp[l][r])$

   2. $dp[l][r]$可以折叠起来形成 num * (重复串), 

      那么$len = 重复串.size(), k = l + len - 1, dp[l][r] = min(dp[l][r], dp[l][k] + 2 + len(num)), 其中num = (r - l + 1) / len$

```cpp
const int MAXN = 105;
int dp[MAXN][MAXN], m[MAXN];

bool check(int l, int r, int len, string &s)
{
    for (int i = l; i <= r; i++)
        if (s[i] != s[(i - l) % len + l])
            return false;
    return true;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    string op;
    cin >> op;
    int n = op.size();
    op = '_' + op;
    memset(dp, 0x3f, sizeof(dp));
    for (int i = 1; i <= n; i++)
    {
        dp[i][i] = 1;
    }
    for (int i = 1; i <= 9; i++)
        m[i] = 1;
    for (int i = 10; i <= 99; i++)
        m[i] = 2;
    m[100] = 3;

    for (int len = 2; len <= n; len++)
    {
        for (int l = 1; l + len - 1 <= n; l++)
        {
            int r = l + len - 1;
            for (int k = l; k + 1 <= r; k++)
            {
                dp[l][r] = min(dp[l][r], dp[l][k] + dp[k + 1][r]);
            }
            for (int k = l; k + 1 <= r; k++)
            {
                int len2 = k - l + 1;
                if (len % len2 != 0)
                    continue;
                if (check(l, r, len2, op))
                {
                    dp[l][r] = min(dp[l][r], dp[l][k] + 2 + m[len / len2]);
                }
            }
        }
    }

    cout << dp[1][n] << endl;
    return 0;
}

```

## P4170 [CQOI2007]涂色

1. 对于两端相同的颜色，可以直接涂一次，然后小区间得到大区间的答案，所以可以用区间dp
2. $讨论dp[l][r], 如果s[l] == s[r]， 我们可以让上一次操作多涂一格即可，就是dp[l][r] = min(dp[l+1][r], dp[l][r-1]),$
3. $如果s[l] != s[r], 那我们就寻找两个小区间进行合并即可，dp[l][r] = min(dp[l][k] + d[k+1][r])$

```cpp

const int MAXN = 55;
int dp[MAXN][MAXN];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    string op;
    cin >> op;
    op = "_" + op;
    memset(dp, 0x3f3f3f3f, sizeof(dp));
    for(int i = 1; i < MAXN; i++)
    {
        dp[i][i] = 1;
    }
    int n = op.size();
    for(int len = 2; len <= n; len++)
    {
        for(int l = 1; l + len - 1 <= n; l++)
        {
            int r = len + l - 1;
            if(op[l] == op[r])
            {
                dp[l][r] = min(dp[l][r-1], dp[l+1][r]);
            }
            else
            {
                for(int k = 1; k + 1 <= r; k++)
                {
                    dp[l][r] = min(dp[l][r], dp[l][k] + dp[k+1][r]);
                }
            }
        }
    }
    cout << dp[1][n] - 1 << endl;
    return 0;
}
```

