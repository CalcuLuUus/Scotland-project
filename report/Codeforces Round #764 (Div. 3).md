# [Codeforces Round #764 (Div. 3)](https://codeforces.com/contest/1624)

## A

只要把最小的加到最大的就行了，反正每次任选几个加

```cpp
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
        int minn = 2e9;
        int n;
        cin >> n;
        ll sum = 2e9;
        int maxx = -1;
        for(int i = 0; i < n; i++)
        {
            cin >>a[i];
            minn = min(a[i], minn);
            maxx = max(a[i], maxx);
            sum += a[i];
        }
        cout << maxx - minn << endl;
    }
    return 0;
}
```



## B

暴力讨论那两个做等差数列看是否能更新第三个即可,注意要正整数

```cpp

bool check(ll a, ll b, ll c)
{
    ll tarc = b + b - a;
    if(tarc / c > 0 && tarc % c == 0) return 1;
    ll tarb = (a+c)/2;
    if((a+c) % 2 == 0 && tarb % b == 0 && tarb / b > 0) return 1;
    ll tara = b - (c - b);
    if(tara % a == 0 && tara / a > 0) return 1;
    return 0;
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
        ll a, b, c;
        cin >> a >>b >>c;
        int flg = check(a, b, c);
        cout << (flg ? "YES" : "NO") << endl;
    }
    return 0;
}

```



## C

因为一些小的数字很容易被很多数字生成，那我们贪心的尽量先构造出大的数字，再构造小的即可

```cpp
const int MAXN = 55;
int vis[MAXN];
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
        memset(vis, 0, sizeof(vis));
        vis[1] = 1;
        for(int i = 0; i < n; i++)
        {
            int num;
            cin >> num;
            while(num > 1)
            {
                if(num <= n && vis[num] == 0)
                {
                    vis[num] = 1;
                    break;
                }
                num /= 2;
            }
        }
        int flg = 1;
        for(int i =1; i <= n; i++)
        {
            if(!vis[i])
            {
                flg = 0;
                break;
            }
        }
        cout << (flg ? "YES" : "NO") << endl;
    }
    return 0;
}
```



## D

我们把这些字母的偶数个抽出来均匀分成m份，剩余的字母看看是否能每一份分一个，形成回文

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
        int n, m;
        cin >> n >> m;
        string op;
        cin >> op;;
        memset(cnt, 0, sizeof(cnt));
        for(int i = 0; i < op.size(); i++)
        {
            cnt[op[i] - 'a']++;
        }
        int odd = 0;
        ll sum = 0;
        for(int i = 0; i < 26; i++)
        {
            if(cnt[i] % 2) odd++;
            sum += cnt[i] / 2;
        }
        ll len = sum / m;
        odd += (sum - m*len) *2;
        if(odd >= m)
        {
            len = len * 2 + 1;
        }
        else
        {
            len = len * 2;
        }
        cout << len << endl;
        
    }
    return 0;
}
```



## E

我们把所有长度为2和长度为3的字符串存起来，更长的字符串可以被长度为2/3的字符串组成

存下所有长度为2/3的字符串后，dfs记忆化搜索/dp判断能否组成

```cpp
typedef pair<pair<int, int>, int> PP;

string final;
map<string ,PP> vis;
map<string ,int> vis2;
vector<PP> ans;
int st[1005];

bool dfs(int pos)
{
    if(st[pos] != -1)
    {
        return st[pos];
    }
    if(pos == final.size())
    {
        return 1;
    }
    bool flg = false;
    string tmp;
    tmp += final[pos];
    if(pos+1 < final.size()) tmp += final[pos+1];
    if(vis2[tmp] == 1)
    {
        ans.push_back(vis[tmp]);
        flg = dfs(pos+2);
        if(flg) return st[pos] = 1;
        else
        {
            ans.pop_back();
        }
    }
    if(pos+2 < final.size()) tmp += final[pos+2];
    if(vis2[tmp] == 1)
    {
        ans.push_back(vis[tmp]);
        flg = dfs(pos+3);
        if(flg) return st[pos] = 1;
        else
        {
            st[pos] = 0;
            ans.pop_back();
        }
    }
    return 0;
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
        vis.clear();
        vis2.clear();
        ans.clear();
        for(int i= 0; i < 1005; i++) st[i] = -1;
        int n, m;
        cin >> n >> m;
        for(int i = 0; i < n; i++)
        {
            string op;
            cin >> op;
            //cout << op << endl;
            
            for(int j = 0; j < m; j++)
            {   
                if(j + 3 <= m)
                {
                    //cout << op.substr(j, 3) << endl;
                    vis[op.substr(j, 3)] = PP(P(j+1, j+3), i+1);
                    vis2[op.substr(j, 3)] = 1;
                }
                if(j + 2 <= m)
                {
                    //cout << op.substr(j, 2) << endl;
                    vis[op.substr(j, 2)] = PP(P(j+1, j+2), i+1);
                    vis2[op.substr(j, 2)] = 1;
                }
            }
        }
        cin >> final;
        dfs(0);
        if(ans.size() == 0)
        {
            cout << -1 << endl;
        }
        else
        {
            cout << ans.size() << endl;
            for(auto x : ans)
            {
                cout << x.first.first << ' ' << x.first.second << ' ' << x.second << endl;
            }
        }
    }
    return 0;
}
```



## F

1000和10的关系很容易想到二分

首先范围一定是在k*n+1 ~ （k+1) * n-1中

定义l = 1， r = n-1

mid = l + r >> 1， x = mid/n

假设mid是我们要找的数字

那么我们把数字加上n - mid%n，就能使得原本的x变成x + 1

如果数字x变成x+1，那么这个数字在mid的右边，否则在左边

```cpp

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n;
    cin >> n;
    int ans = 1;
    int l = 1, r = n-1;
    while(l < r)
    {
        int mid = (l + r + 1) >> 1;
        int x = n - mid % n;
        int y = mid / n + 1;
        // dbg(l);
        // dbg(r);
        // dbg(mid);
        // dbg(x);
        // dbg(y);
        cout << "+ " << x << endl;
        int ret;
        cin >> ret;
        if(ret == y)
        {
            l = mid;
        }
        else
        {
            r = mid - 1;
        }
        //cout << "before " << l <<  ' ' << r << ' ' << x << endl;
        l += x;
        r += x;
        // cout << "after " << l <<  ' ' << r << endl;
    }
    cout << "! " << l << endl;
    return 0;
}
```



## G

从高到底按位贪心，我们首先贪心某一位是0，那么我们就删除边权二进制这一位为1的边，看看是否还能组成mst，如果可以，那么我们就删除这条边，否则答案在这一位与上1，表明在mst上一定有一条边边权在这一位上为1

```cpp
struct node
{
    int u, v, w;
    node(){};
    node(int a, int b, int c):u(a), v(b), w(c)
    {
    }
};

const int MAXN = 2e5+5;
vector<node> v;


int fa[MAXN];
void init(int n){
    for(int i = 0; i <= n; i++)
    {
        fa[i] = i;
    }
}

int find_root(int x)
{
    return x == fa[x] ? x : fa[x] = find_root(fa[x]);
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
        int n, m;
        cin >> n >> m;
        v.clear();
        for(int i = 0; i < m; i++)
        {
            int st, ed ,w;
            cin >> st >> ed >> w;
            v.push_back(node(st ,ed , w));
        }
        int ans = 0;
        for(int i = 30 ;i >= 0; i--)
        {
            init(n);
            vector<node> nv;
            int cnt = 0;
            for(int j = 0; j < v.size(); j++)
            {
                if((v[j].w & (1 << i)) == 0)
                {
                    nv.push_back(v[j]);
                    int st = v[j].u, ed = v[j].v;
                    if(find_root(st) != find_root(ed))
                    {
                        fa[find_root(st)] = find_root(ed);
                        cnt++;
                    }
                }
            }
            if(cnt >= n-1)
            {
                v = nv;
            }
            else
            {
                ans |= (1 << i);
            }
        }
        cout << ans << endl;
    }
    return 0;
}

```

