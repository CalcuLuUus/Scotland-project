# 树形dp

## summary

树形dp两种写法，用$dp[u] = \sum_{v \in son_u} dp[v]$作为例子

1. 采取记忆化的写法，将状态用参数写进dp

   例如

   ```cpp
   int dfs(u, j)
   {
       if(dp[u][j]) return dp[u][j];
       int ans = 0;
       for(int son : son of u)
       {
           ans += dfs(son, j);
       }
       return dp[u][j] = ans;
   }
   ```

   

2. 直接dfs处理好儿子的dp数组，再处理父亲的

   比如

   ```cpp
   void dfs(u)
   {
       for(int son : son of u)
       {
           dfs(son);
           for(j)
           {
               dp[u][j] += dp[son][j];
           }
       }
   }
   ```

## P1273 有线电视网

树形背包

```cpp
const int MAXN = 3005;
vector<P> edge[MAXN];
int dp[MAXN][MAXN], p[MAXN];
int t[MAXN];

int n, m;
int dfs(int u)
{
    if(u > n - m)
    {
        dp[u][1] = p[u];
        return 1;
    } 
    int sum = 0;
    for(int i = 0; i < edge[u].size(); i++)
    {
        int v = edge[u][i].second, w = edge[u][i].first;
        int cnt = dfs(v);
        sum += cnt;
        for(int j = 1; j <= sum; j++) t[j] = dp[u][j];
        for(int j = 0; j <= sum; j++)
        {
            for(int k = max(0, j + cnt - sum); k <= min(cnt, j); k++)
            {
                dp[u][j] = max(dp[u][j], t[j-k]+dp[v][k]-w);
            }
        }
    }
    return sum;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    memset(dp, ~0x3f, sizeof(dp));
    cin >> n >> m;
    for(int i = 1; i <= n - m; i++)
    {
        int k;
        cin >> k;
        while(k--)
        {
            int a, c;
            cin >> a >> c;
            edge[i].push_back(P(c, a));
        }
    }
    for(int i = n-m+1; i <= n; i++)
    {
        cin >> p[i];
    }

    dfs(1);

    for(int i = m; i >= 0; i--)
    {
        if(dp[1][i] >= 0)
        {
            cout << i << endl;
            return 0;
        }
    }

    return 0;
}
```

## P2014 [CTSC1997] 选课

树形背包

```cpp
const int MAXN = 305;
int dp[MAXN][MAXN];
int n, m;
vector<int> edge[MAXN];
int s[MAXN];

void dfs(int u)
{
    for(int  i= 0; i < edge[u].size(); i++)
    {
        int to = edge[u][i];
        dfs(to);
        for(int j = m+1; j >= 1; j--)
        {
            for(int k = 0; k < j; k++)
            {
                dp[u][j] = max(dp[u][j], dp[u][j - k] + dp[to][k]);
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
    {
        int ki, si;
        cin >> ki >> si;
        edge[ki].push_back(i);
        dp[i][1] = si;
    }
    dfs(0);
    cout << dp[0][m+1];

    return 0;
}
```

## P2585 [ZJOI2006]三色二叉树

先建树，考虑点涂不涂绿色，$dp[u][0/1]$为u点不涂/涂绿色子树最多绿色节点

```cpp
const int MAXN = 1e6+5;
int tree[MAXN][2];

int tot = 0;
string op;

void build(int root)
{
    tot++;
    if(op[root] == '0') return ;
    else if(op[root] == '1')
    {
        tree[root][0] = root + 1;
        build(root+1);
    }
    else
    {
        tree[root][0] = root + 1;
        build(root + 1);
        tree[root][1] = tot + 1;
        build(tot + 1);
    }
}

int dp[MAXN][2];
int dp2[MAXN][2];

void dfs(int u)
{
    int lch = tree[u][0], rch = tree[u][1];
    if(lch)
    {
        dfs(lch);
    }
    if(rch)
    {
        dfs(rch);
    }
    dp[u][0] = max(dp[lch][0] + dp[rch][1], dp[lch][1] + dp[rch][0]);
    dp[u][1] = dp[lch][0] + dp[rch][0] + 1;

    dp2[u][0] = min(dp2[lch][0] + dp2[rch][1], dp2[lch][1] + dp2[rch][0]);
    dp2[u][1] = dp2[lch][0] + dp2[rch][0] + 1;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> op;
    op = "-" + op;
    build(1);
    dfs(1);
    cout << max(dp[1][0], dp[1][1]) << ' ' << min(dp2[1][0], dp2[1][1])  << endl;

    return 0;
}
```

## P3698 [CQOI2017]小Q的棋盘

考虑可能可以走回来，$dp[u][j][0/1]$表示u节点出发经过j节点最后是回到u的数

```cpp
const int MAXN = 105;
vector<int> edge[MAXN];
int dp[MAXN][MAXN][2];
int n, v;

void dfs(int u, int fa)
{
    dp[u][0][1] = dp[u][0][0] = 1;
    for(int i = 0; i < edge[u].size(); i++)
    {
        int v = edge[u][i];
        if(v == fa) continue;
        dfs(v, u);
        for(int j = n; j >= 0; j--)
        {
            for(int k = 0; k <= j; k++)
            {
                dp[u][j][0] = max({dp[u][j][0], dp[u][j-k][0] + dp[v][k-2][1], dp[u][j-k][1] + dp[v][k-1][0]});
                dp[u][j][1] = max(dp[u][j][1], dp[u][j-k][1] + dp[v][k-2][1]);
            }   
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> v >> n;
    for(int i = 1; i < v; i++)
    {
        int x, y;
        cin >> x >> y;
        edge[x].push_back(y);
        edge[y].push_back(x);
    }
    dfs(0, 0);
    int ans = 0;
    for(int i = 0; i <= n; i++)
    {
        ans = max(ans, dp[0][i][0]);
    }
    cout << ans <<endl;
    return 0;
}
```