# 2021 CCPC Guilin

## E

赛中想到了思路，但是并没有想到怎么找环

没想到直接dijkstra暴力n次

找$$dis[i][j] + dis[j][i]$$就可以了

```cpp
const int MAXN = 2005;
int n, m;
vector<P> edge[MAXN];
vector<int> dis[MAXN];

void dijkstra(int id)
{
    // cout << id << endl;
    priority_queue<P, vector<P>, greater<P>> q;
    dis[id][id] = 0;
    q.push(P(dis[id][id], id));
    while(!q.empty())
    {
        P now = q.top();
        q.pop();
        if(dis[id][now.second] < now.first) continue;
        
        for(int i = 0; i < edge[now.second].size(); i++)
        {
            P nxt = edge[now.second][i];
            int to = nxt.second, w = nxt.first;
            if(dis[id][to] > dis[id][now.second] + w)
            {
                dis[id][to] = dis[id][now.second] + w;
                q.push(P(dis[id][to], to));
            }
        }
    }
}

signed main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int C;
    cin >> n >> m >> C;
    int flg = 0;
    for(int i = 0; i <= n; i++) dis[i] = vector<int>(n+5, 0x3f3f3f3f);
    for(int i = 0; i < m; i++)
    {
        int x, y;
        ll c;
        cin >> x >> y >> c;
        if(c > C) continue;
        if(c <= C) flg = 1;
        edge[x].push_back(P(c, y));
    }
    if(!flg)
    {
        cout << 0 << endl;
        return 0;
    }
    for(int i = 1; i <= n; i++)
    {
        dijkstra(i);
    }
    int ans = 0;
    for(int i = 1; i <= n; i++)
    {
        for(int j = i + 1; j <= n; j++)
        {
            if(dis[i][j] + dis[j][i] <= C)
            {
                ans = 1;
                break;
            }
        }
    }
    cout << ans + 1 << endl;
    
    return 0;
}
```

## G

二分答案，尽量让0被最近的1干掉

```cpp
const int N = 1e6 + 5;
int T, n, m;
int va[N];
char s[N];
int suml[N], sumr[N];
int vis[N];
const int inf = 0x3f3f3f3f;
bool check(int mid)
{
    for (int i = 1; i <= n; i++)
        vis[i] = 0;
    for (int i = 1; i <= n; i++)
    {
        if (s[i] == '0')
        {
            int minn = min(i - suml[i], sumr[i] - i) + 1;
            if (minn <= mid)
                continue;
            if (minn == mid + 1) //最多能处理这些
            {
                if (suml[i] >= 1 && !vis[suml[i]]) //尽量用左边的1，右边的留给别人，这样最贪心
                {
                    vis[suml[i]] = 1;
                    continue;
                }
                if (sumr[i] <= n && !vis[sumr[i]])
                {
                    vis[sumr[i]] = 1;
                    continue;
                }
            }
            return false;
        }
    }
    return true;
}

signed main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> T;
    while (T--)
    {
        cin >> n;
        cin >> s + 1;
        int maxnl = -inf, maxnr = inf;
        for (int i = 1; i <= n; i++)
        {
            if (s[i] == '0')
                suml[i] = maxnl;
            else
                maxnl = i;
        }
        for (int i = n; i >= 1; i--)
        {
            if (s[i] == '0')
                sumr[i] = maxnr;
            else
                maxnr = i;
        }
        int l = 0, r = n;
        while (l < r)
        {
            int mid = (l + r) >> 1;
            if (check(mid))
                r = mid;
            else
                l = mid + 1;
        }
        cout << l << "\n";
    }
    return 0;
}

```

## D

一开始想的是，数字大的先移动，移动到原本的位置

但是通过某种移动可以使得状况更优

比如a = 4 5 1 2 3     b  = 1 3 2 4 5

如果先移动5 和 4的话，a = 1 2 3 4 5, 就不可以移动了

但是如果先移动1， 1 5 4 2 3， 1 5 2 4 3， 1 3 2 5 4， 1 3 2 5 4，每一次都替换最小的地方，让大的在前面

说明我们要尽量保证大的数字在前面，可以留下更多操作空间

我们从小到大移动数字，移动策略如下：

> 从小到大先把1移到它要去的地方然后再移2一直到n，但是不是直接移动
>
> 比如现在1的位置是5最终要到位置1，你就先区间查询1-4位置的最小值，然后把1移到查询的位置
>
> 然后这个区间的最小值移动到了5这个位置然后继续查询位置1到当前位置的最小值然后再往前移动
>
> 这样次小的可以跟最小的再移动5 4 2 3 1先把1移动到3这个位置5 4 1 3 2你看看除了1往前走了

```cpp
const int MAXN = 2025;

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
        vector<int> posa(n + 5, 0), posb(n +5 , 0), a(n+ 5, 0), b(n+5, 0);
        for(int i = 1; i <= n; i++)
        {
            int num;
            cin >> num;
            a[i] = num;
            posa[num] = i;
        }
        for(int i = 1; i <= n; i++)
        {
            int num;
            cin >> num;
            b[i] = num;
            posb[num] = i;
        }
        vector<P> ans;
        int fflg = 1;
        for(int cur = 1; cur <= n; cur++)
        {
            while(posa[cur] > posb[cur])
            {
                int l = posb[cur], r = posa[cur] - 1;
                int minn = 0x3f3f3f, minpos = 0;
                for(int i = l; i <= r; i++)
                {
                    if(a[i] == b[i])
                    {
                        continue;
                    }
                    if(a[i] > cur && a[i] < minn)
                    {
                        minn = a[i];
                        minpos = i;
                    }
                }
                if(minpos == 0) break;
                ans.push_back(P(minpos, posa[cur]));
                swap(a[posa[cur]], a[minpos]);
                swap(posa[cur], posa[minn]);
            }
        }

        for(int i = 1; i <= n; i++)
        {
            if(a[i] != b[i])
            {
                fflg = 0;
                break;
            }
        }
        if(fflg == 0)
        {
            cout << -1 << endl;
        }
        else
        {
            cout << ans.size()<< endl;
            for(auto x : ans)
            {
                cout << x.first << ' ' << x.second << endl;
            }
        }
    }
    return 0;
}

```

## B

对于a[]和b[]的改动，我们可以更改sum[]

对于sum[]建立线段树

核心思想就是当我修改一个数的时候，能快速查出前面有多少个0， 多少个9

从而实现快速进位或快速借位

要做到区间修改，区间查询

可以区间查询最长的后缀0和后缀9

快速修改可以用懒标记

```cpp
#define lch p << 1
#define rch p << 1 | 1

const int MAXN = 1e6+5;
struct node
{
    int val;
    int suf9;
    int suf0;
    int tag0, tag9;
    int l, r, len;
};

node t[MAXN << 2];

int a[MAXN], b[MAXN], sum[MAXN];

void update(int p)
{
    if(t[rch].suf0 == t[rch].len)
    {
        t[p].suf0 = t[rch].len + t[lch].suf0;
    }
    else
    {
        t[p].suf0 = t[rch].suf0;
    }
    if(t[rch].suf9 == t[rch].len)
    {
        t[p].suf9 = t[rch].len + t[lch].suf9;
    }
    else
    {
        t[p].suf9 = t[rch].suf9;
    }
}

void spread(int p)
{
    if(t[p].tag0)
    {
        t[lch].val = t[rch].val = 0;
        t[lch].suf0 = t[lch].len;
        t[rch].suf0 = t[rch].len;
        t[lch].suf9 = 0;
        t[rch].suf9 = 0;
        t[lch].tag0 = t[rch].tag0 = 1;
        t[lch].tag9 = t[rch].tag9 = 0;
        t[p].tag0 = 0;
    }
    if(t[p].tag9)
    {
        t[lch].val = t[rch].val = 9;
        t[lch].suf9 = t[lch].len;
        t[rch].suf9 = t[rch].len;
        t[lch].suf0 = 0;
        t[rch].suf0 = 0;
        t[lch].tag9 = t[rch].tag9 = 1;
        t[lch].tag0 = t[rch].tag0 = 0;
        t[p].tag9 = 0;
    }
}

void build(int l, int r, int p)
{
    t[p].l = l, t[p].r = r, t[p].len = r - l + 1;
    if(l == r)
    {
        t[p].val = sum[l];
        t[p].suf0 = (sum[l] == 0), t[p].suf9 = (sum[r] == 9);
        return ;
    }
    int mid = l + r >> 1;
    build(l, mid, lch);
    build(mid+1, r, rch);
    update(p);
}


int ask9(int ql, int qr, int l, int r, int p)
{
    if(ql <= l && r <= qr)
    {
        return t[p].suf9;
    }
    spread(p);
    int mid = l + r >> 1;
    if(qr > mid)
    {
        int ans = ask9(ql, qr, mid+1, r, rch);
        if(ans ==  min(qr, t[rch].r) - t[rch].l + 1)
        {
            ans += ask9(ql, qr, l, mid, lch);
        }
        return ans;
    }
    else
    {
        return ask9(ql, qr, l, mid, lch);
    }
}
 
int ask0(int ql, int qr, int l, int r, int p)
{
    if(ql <= l && r <= qr)
    {
        return t[p].suf0;
    }
    spread(p);
    int mid = l + r >> 1;
    if(qr > mid)
    {
        int ans = ask0(ql, qr, mid+1, r, rch);
        if(ans ==  min(qr, t[rch].r) - t[rch].l + 1)
        {
            ans += ask0(ql, qr, l, mid, lch);
        }
        return ans;
    }
    else
    {
        return ask0(ql, qr, l, mid, lch);
    }
}

int ask(int x, int l, int r, int p)
{
    if(l == r)
    {
        return t[p].val;
    }
    spread(p);
    int mid = l + r >> 1;
    if(x <= mid)
    {
        return ask(x, l ,mid, lch);
    }
    else
    {
        return ask(x, mid+1, r, rch);
    }
}

void change9(int ql, int qr, int l, int r, int p)
{
    if(ql <= l && r <= qr)
    {
        t[p].tag9 = 1;
        t[p].val = 9;
        t[p].suf9 = t[p].len;
        t[p].tag0 = 0;
        t[p].suf0 = 0;
        return ;
    }
    spread(p);
    int mid = l + r >> 1;
    if(ql <= mid)
    {
        change9(ql, qr, l, mid, lch);
    }
    if(mid < qr)
    {
        change9(ql, qr, mid+1, r, rch);
    }
    update(p);
}

void change0(int ql, int qr, int l, int r, int p)
{
    if(ql <= l && r <= qr)
    {
        t[p].tag0 = 1;
        t[p].val = 0;
        t[p].suf0 = t[p].len;
        t[p].tag9 = 0;
        t[p].suf9 = 0;
        return ;
    }
    spread(p);
    int mid = l + r >> 1;
    if(ql <= mid)
    {
        change0(ql, qr, l, mid, lch);
    }
    if(mid < qr)
    {
        change0(ql, qr, mid+1, r, rch);
    }
    update(p);
}

void change(int x, int l, int r, int p, int val)
{
    if(l == r)
    {
        t[p].val = val;
        t[p].suf9 = (val == 9);
        t[p].suf0 = (val == 0);
        return ;
    }
    spread(p);
    int mid = l + r >> 1;
    if(x <= mid)
    {
        change(x, l, mid, lch, val);
    }
    else
    {
        change(x, mid+1, r, rch, val);
    }
    update(p);
}



int n, m;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    //// cout.tie(0);
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i++)
    {
        scanf("%1d", &a[i]);
    }
    for(int i = 1; i <= n; i++)
    {
        scanf("%1d", &b[i]);
    }
    for(int i = n; i >= 1; i--)
    {
        sum[i-1] = ((sum[i] + a[i] + b[i]) >= 10);
        sum[i] = (sum[i] + a[i] + b[i]) % 10;
    }
    sum[0] = 0;
 
    build(1, n, 1);
    while(m--)
    {
        int r, c, d;
        scanf("%d%d%d", &r, &c, &d);
        int dis = 0;
        if(r == 1)
        {
            dis = d - a[c];
            a[c] += dis;
        }
        else
        {
            dis = d - b[c];
            b[c] += dis;
        }

        int res = ask(c, 1, n, 1) + dis;

        if(dis == 0)
        {
            printf("%d 0\n", res);
            continue;
        }

        if(0 <= res && res <= 9 || c == 1)
        {
            res = (res + 10) % 10; // 有可能把第一位减成负数了
            change(c, 1, n, 1, res);
            printf("%d 2\n", res);
            continue;
        }

        if(res > 9)
        {
            int len = ask9(1, c-1, 1, n, 1);
            change(c, 1, n, 1, res - 10);
            if(len)
            {
                change0(c - len, c - 1, 1, n, 1);
            }
            if(c - len - 1 >= 1)
            {
                change(c - len - 1, 1, n, 1, ask(c-len-1,  1, n, 1) + 1);
                printf("%d %d\n", res - 10, len + 3);
            }
            else
            {
                printf("%d %d\n", res - 10, len + 2);
            }
        }
        else if(res < 0)
        {

            int len = ask0(1, c-1, 1, n, 1);
            // cout << len << endl
            change(c,1, n, 1, res + 10);
            if(len)
            {
                change9(c - len, c - 1, 1, n, 1);
            }
            if(c - len - 1 >= 1)
            {
                change(c - len - 1, 1, n, 1, ask(c-len-1, 1, n, 1) - 1);
                printf("%d %d\n", res + 10, len + 3);
            }
            else
            {
                printf("%d %d\n", res + 10, len + 2);
            }
        }
        
    }
    return 0;
}
```

## K

先走最短路，若有多条最短路则走花费最小的

最短路直接用bfs算出来

然后我们按照最短路搜索

如果dis[to] == dis[u] + 1, 那么我们会经过（u， to）这条边

搜索，开一个cnt记录第i家公司路过多少次了

复杂度：

我们把这张图按照距离看成一个分层图

当分层图每一层的点数一样的时候，负责度最高

设一层有x个点，那么有n/x层

复杂度就是$O(x^{n/x})$

当x = 3取到最大值

```cpp
int n, m;
const int MAXN = 55 * 55;
int dis[MAXN];
vector<P> edge[MAXN];
int cnt[MAXN];
int w[MAXN];
int cost[MAXN];

void bfs(int st)
{
    dis[st] = 1;
    queue<int> q;
    q.push(st);
    while(!q.empty())
    {
        int now = q.front();
        q.pop();
        for(int i = 0; i < edge[now].size(); i++)
        {
            int to = edge[now][i].second;
            if(!dis[to])
            {
                dis[to] = dis[now] + 1;
                q.push(to);
            }
        }
    }

}

void dfs(int u, int ans = 0)
{
    if(cost[u] > ans) cost[u] = ans;
    for(int i = 0; i < edge[u].size(); i++)
    {
        int c = edge[u][i].first, to = edge[u][i].second;
        if(dis[to] != dis[u] + 1) continue;
        ++cnt[c];
        dfs(to, ans + cnt[c] * w[c]);
        --cnt[c];
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> m;
    for(int i = 1; i <= m; i++)
    {
        cin >> w[i];
    }
    for(int i = 1; i <= m; i++)
    {
        int x, y, c;
        cin >>x >> y >> c;
        edge[x].push_back(P(c, y));
        edge[y].push_back(P(c, x));
    }
    memset(cost, 0x3f, sizeof(cost));
    
    bfs(1);
    dfs(1);
    for(int i = 2; i <= n; i++)
    {
        cout << cost[i] << endl;
    }
    return 0;
}

```

