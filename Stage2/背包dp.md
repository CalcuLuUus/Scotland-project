# 背包dp

## P5322 [BJOI2019] 排兵布阵

非常容易发现，$dp[i][j-2*a[i][k]-1] = max(dp[i][j], dp[i-1][j] + i)$

但是这样转移不知道能战胜多少个对手，还要再开一组记录，比较麻烦

所以先排序，然后看看是第k大，就说明能够战胜k个对手

转移优化为先排序，再$dp[i][j-2*a[i][k]-1] = max(dp[i][j], dp[i-1][j] + k*i)$

```cpp

const int MAXN = 105, MAXM = 2e4+5;

int s, n, m;
int dp[MAXN][MAXM];
int a[MAXN][MAXN], sum[MAXN];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> s >> n >> m;
    for(int i = 1; i <= s; i++)
    {
        for(int j = 1; j <= n; j++)
        {
            cin >> a[j][i];
        }
    }
    

    for(int i = 1; i <= n; i++)
    {
        sort(a[i] + 1, a[i] + s + 1);
    }
    for(int i = 1; i <= n; i++)
    {
        for(int j = m; j >= 0; j--)
        {
            dp[i][j] = max(dp[i][j], dp[i-1][j]);
        }
        for(int j = m; j >= 0; j--)
        {
            for(int k = 1; k <= s; k++)
            {
                if(j > 2 * a[i][k])
                {
                    dp[i][j - 2*a[i][k] - 1] = max(dp[i - 1][j] + k * i, dp[i][j - 2*a[i][k] - 1]);
                }
            }
        }
    }
    

    int ans = -1;
    for(int i = 0; i <= m; i++)
    {
        ans = max(ans, dp[n][i]);
    }
    cout << ans << endl;
    return 0;
}
```

## P1156 垃圾陷阱

很容易想出，对于每个垃圾，我们抉择吃还是垫

那么$dp[i][j]$代表第i个垃圾的时候，在j高度有多少体力

$dp[i][j + h] = max(dp[i][j], dp[i-1][j] - tdis)$垫在脚下

$dp[i][j] = max(dp[i][j], dp[i-1][j] + f - tdis)$吃

tdis是两个垃圾的时间间隔所消耗的体力，f是垃圾补充的体力

注意初始化的时候要初始化负无穷，因为$ans2 = max(ans2, dp[i][0] + t);$的时候$dp[i][0] == -1$若t足够大仍能更新ans2，但是这个状态不应该存在，因为等于-1的时候说明这个状态无法到达

或者可以这样写

```cpp
if(dp[i][0] != -1) ans2 = max(ans2, dp[i][0] + t);
else ans2 = ans2;
```

```cpp
const int MAXN = 105;
int dp[MAXN][MAXN];
tuple<int, int, int> a[MAXN];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int d, g;
    cin >> d >> g;
    for (int i = 1; i <= g; i++)
    {
        int t, f, h;
        cin >> t >> f >> h;
        a[i] = make_tuple(t, f, h);
    }
    memset(dp, -1, sizeof(dp));
    sort(a + 1, a + g + 1);
    a[0] = make_tuple(0, 0, 0);
    dp[0][0] = 10;
    int ans2 = -1;
    for (int i = 1; i <= g; i++)
    {
        int f = get<1>(a[i]), h = get<2>(a[i]), t = get<0>(a[i]), tt = get<0>(a[i - 1]), tdis = t - tt;
        for (int j = d; j >= 0; j--)
        {
            if (dp[i - 1][j] < tdis)
                continue;
            if (j + h >= d)
            {
                cout << t << endl;
                return 0;
            }
            dp[i][j + h] = max(dp[i][j + h], dp[i - 1][j] - tdis);
            dp[i][j] = max(dp[i][j], dp[i - 1][j] + f - tdis);
        }
        if(dp[i][0] != -1) ans2 = max(ans2, dp[i][0] + t);
        else ans2 = ans2;
    }
    cout << ans2 << endl;
    return 0;
}
```











