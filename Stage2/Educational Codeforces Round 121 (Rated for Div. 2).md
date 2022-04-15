# Educational Codeforces Round 121 (Rated for Div. 2)

## C

我的做法是先逆序排除掉会跳过的怪物，然后正序排除掉会跳过的，但是写得很丑

```cpp
const int MAXN = 105;
int k[MAXN], h[MAXN], del[MAXN];
set<P> s;
ll getsum(ll st, ll ed)
{
    return (st + ed) * (ed - st + 1) / 2;
}
int main()
{
    //FIN;
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin >> t;
    while(t--)
    {
        int n;
        s.clear();
        cin >> n;
        vector<P> v;
        for(int i = 0; i < n; i++)
        {
            cin >> k[i];
            del[i] = 0;
        }
        for(int i = 0; i < n; i++)
        {
            cin >> h[i];
            v.push_back(P(k[i], h[i]));
        }
        for(int i  = n-1; i >= 0; i--)
        {
            if(del[i]) continue;
            int K = v[i].first, H = v[i].second;
            int pos = lower_bound(v.begin(), v.end(), P(K - H + 1, 1)) - v.begin();
            for(int j = pos; j < i; j++)
            {
                if(v[j].second <= H - (K - v[j].first))
                {
                    del[j] = 1;
                }
            }
        }
        vector<P> vv;
        for(int i = 0; i < n; i++)
        {
            if(!del[i]) vv.push_back(v[i]);
            del[i] = 0;
        }
        for(int i = 0; i < vv.size(); i++)
        {
            if(del[i]) continue;
            int K = vv[i].first, H = vv[i].second;
            int rmost = i;
            for(int j = i+1; j < vv.size(); j++)
            {
                int st = vv[j].first - vv[j].second + 1;
                if(st <= K)
                {
                    del[i] = 1;
                    rmost = max(rmost, j);
                    vv[j].second = H + vv[j].first - K;
                }
            }
            for(int j = i; j < rmost; j++) del[j] = 1;
        }
        v.clear();
        for(int i = 0; i < vv.size(); i++)
        {
            if(del[i] == 0) v.push_back(vv[i]);
        }
        ll ans = 0;
        for(int i = 0; i < v.size(); i++)
        {
            ans += getsum(1, v[i].second);
        }
        cout << ans << endl;
    }
    return 0;
}
```

## D

因为要2的幂次，所以我们直接枚举长度即可，枚举最左端的长度，枚举最右端的长度，从而得到数字x,y，正如题目所划分的一样，划分成[0, x), [x, y), [y, n]，upperbound找x, lowerbound找y， 然后模拟即可

```cpp

const int MAXN = 2e5+5;

int cal(int x)
{
    if(x == 0) return 1;
    return (1 << ((int)ceil(log2(x)))) - x;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int t;
    cin >> t;
    while(t--)
    {
        int n;
        cin >> n;
        vector<int> a(n+5, 0), vis(n+1, 0);
        for(int i = 1; i <= n; i++)
        {
            cin >> a[i];
            vis[a[i]]++;
        }
        if(n == 1)
        {
            cout << 2 << endl;
            continue;
        }

        for(int i = 1; i <= n; i++)
        {
            vis[i] += vis[i-1];
            
        }
        ll tot = vis[n];
        ll ans = 1e18;
        for(int l = 1; l <= n; l <<= 1)
        {
            int x = upper_bound(vis.begin(), vis.end(), l) - vis.begin();
            for(int r = 1; r <= n; r <<= 1)
            {
                int y = lower_bound(vis.begin(), vis.end(), tot - r) - vis.begin();
                if(x - 1 >= y + 1) break;
                ans = min(ans, l - vis[x - 1] + r - (tot - vis[y]) + cal(vis[y] - vis[x-1]));
            }
        }
        cout << ans << endl;
    }
    return 0;
}
```

## E

1. 和黑点相邻的均可

2. 如果一个点

   1. 他的儿子是黑点（情况1）
   2. 他的儿子的子树有两个黑点并且这个儿子可以达到黑点（dfs1， 父亲向儿子方向)
   3. 反向2一次，看另一半树(dfs2, 儿子向父亲方向)

   那么他可以到达黑点

```cpp
const int MAXN = 3e5+5;
int a[MAXN];
vector<int> edge[MAXN];
int dp[MAXN], ans[MAXN];

void dfs(int u, int Fa)
{
    for(int to : edge[u])
    {
        if(to != Fa)
        {
            dfs(to, u);
            dp[u] += dp[to];
            if(ans[to] && dp[to] >= 2)
            {
                ans[u] = 1;
            }
        }
    }
}

int tot = 0; // black cnt
void dfs2(int u, int fa)
{
    for(int to : edge[u])
    {
        if(to != fa)
        {
            if(tot - dp[to] >= 2 && ans[u])
            {
                ans[to] = 1;
            }
            dfs2(to, u);
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n;
    cin >> n;
    for(int i = 1; i <= n; i++)
    {
        cin >> a[i];
        tot += a[i];
        dp[i] = a[i];
    }
    for(int i = 0; i < n-1; i++)
    {
        int x, y;
        cin >> x >> y;
        edge[x].push_back(y);
        edge[y].push_back(x);
    }

    //st 1
    for(int i = 1; i <= n; i++)
    {
        if(a[i])
        {
            ans[i] = 1;
            for(auto to : edge[i])
            {
                ans[to] = 1;
            }
        }
    }

    //st 2
    dfs(1, 0);
    dfs2(1, 0);

    for(int i = 1; i <= n; i++)
    {
        cout << ans[i] << ' ';
    }
    cout << endl;
    
    return 0;
}
```

