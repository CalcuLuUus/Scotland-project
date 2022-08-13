# [Codeforces Round #765 (Div. 2)](https://codeforces.com/contest/1625)

## A

贪心即可

这一位1多就置1,0多就0

```cpp

int cnt[30];
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
        cin >> n;
        int l;
        cin >> l;
        vector<int> v;
        memset(cnt, 0, sizeof(cnt));
        for(int i = 0; i < n; i++)
        {
            int num;
            cin >> num;
            v.push_back(num);
        }
        for(int x : v)
        {
            int j = 0;
            while(x)
            {
                if(x & 1)
                {
                    cnt[j]++;
                }
                j++;
                x >>= 1;
            }
        }
        int ans = 0;
        for(int i = l; i >= 0; i--)
        {
            if(cnt[i] > n / 2)
            {
                ans = ans * 2 + 1;
            }
            else
            {
                ans = ans * 2;
            }
            
        }
        cout << ans << endl;
    }
    return 0;
}

```



## B

找到两个离得最近的相同的数字，同时向左向右拓展区间，看看能拓展多长，取max

```cpp
const int MAXN = 150005;
vector<int> v;

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
        cin >> n;
        v.clear();
        for(int i = 0; i < MAXN; i++)
        {
            v.push_back(-1);
        }
        int ans = -1;
        for(int i = 0; i < n ;i++)
        {
            int num;
            cin >> num;
            if(v[num] == -1)
            {
                v[num] = i;
            }
            else
            {
                ans = max(ans, n - (i - v[num]));
                v[num] = i;
            }
        }
        cout << ans << endl;

    }
    return 0;
}
```



## C

dp

```cpp
ll dp[505][505]; // 到i站点的时间 之前删了j
ll a[505], speed[505];


int main()
{
    //FIN;
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n, k, l;
    cin >> n >> l >> k;
    for(int i = 0; i < n; i++)
    {
        cin >> a[i];
    }
    for(int i = 0; i < n; i++)
    {
        cin >> speed[i];
    }
    a[n] = l;
    
    for(int i = 1; i <= n; i++)
    {
        for(int l = k; l >= 0; l--)
        {
            dp[i][l] = 1e18;
            for(int j = 1; j <= i; j++)
            {
                if(l-j+1 >= 0)
                dp[i][l] = min(dp[i][l], dp[i-j][l-j+1] + speed[i-j] * (a[i] - a[i-j]));
            }
        }
    }
    ll ans = 0x3f3f3f3f3f3f3f3f;
    for(int i = 0; i <= k; i++)
    {
        ans = min(ans,dp[n][i]);
    }
    cout << ans << endl;
    return 0;
}
```



## D

首先，我们把k看成二进制形式，比k高的我们看成pre，k的位看成suf

比如，k是10，二进制是1010，那我们把低4位看成suf，第5-31位看成pre

那么，如果两个数拥有不同的pre，那么他们异或一定是大于k的

如果两个数拥有相同的pre，那么他们有可能异或大于k，也有可能小于k

我们按照pre进行分组，每一组至少可以选一个，然后组内求异或最大值，如果异或最大值大于等于k，那么这个组可以选两个

分组用map，异或极值用trie

注意要特判0

```cpp
const int MAXN = 3e5 + 5;
int a[MAXN];
map<int, vector<P>> mv;
vector<int> s;

int nex[30 * MAXN][2], cnt;
int exist[30 * MAXN];

void init()
{
    for(int i = 0; i <= cnt; i++)
    {
        nex[i][0] = nex[i][1] = exist[i] = 0;
    }
    cnt = 0;
}

void insert(int x, int id)
{
    int p = 0;
    for(int i = 30; i >= 0; i--)
    {
        bool c = x >> i & 1;
        if(!nex[p][c])
        {
            nex[p][c] = ++cnt;
        }
        p = nex[p][c];
    }
    exist[p] = id;
}
bool find(int x, int id, int k)
{
    int p = 0;
    int ans = 0;
    for(int i = 30; i >= 0; i--)
    {
        bool c = x >>i & 1;
        if(nex[p][c ^ 1])
        {
            p = nex[p][c ^ 1];
            ans |= ((c ^ 1) << i);
        }
        else
        {
            p = nex[p][c];
            ans |= (c << i);
        }
    }
    if ((ans ^ x) >= k)
    {
        s.push_back(id);
        s.push_back(exist[p]);
        return exist[p];
    }
    return 0;
}


int main()
{
    //FIN;
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n, k;
    cin >> n >> k;
    for (int i = 0; i < n; i++)
    {
        cin >> a[i];
    }
    int mask = 0;
    if(k == 0)
    {
        cout << n << endl;
        for(int i = 1; i <= n; i++){
            cout << i << ' ';
        }
        cout << endl;
        return 0;
    }
    int _k = k;
    while(_k)
    {
        mask = mask << 1 | 1;
        _k >>= 1;
    }
    mask ^= 0x7fffffff;
    for(int i = 0; i < n; i++)
    {
        mv[a[i] & mask].push_back(P(a[i], i));
    }
    ll ans = 0;
    for(auto it = mv.begin(); it != mv.end(); it++)
    {
        int pre = it -> first;
        init();
        for(auto x : it->second)
        {
            insert(x.first, x.second);
        }
        int flg = 0;
        for(auto x : it->second)
        {
            flg = find(x.first, x.second, k);
            if(flg) break;
        }
        if(!flg)
        {
            s.push_back((it->second)[0].second);
        }
        ans += flg + 1;
    }
    if(ans == 1)
    {
        cout << -1 << endl;
        return 0;
    }
    cout << ans << endl;
    for(int x : s)
    {
        cout << x+1 << ' ';
    }
    cout << endl;
    return 0;
}

```



