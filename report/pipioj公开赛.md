# [PIPIOJ公开赛][http://www.pipioj.online/contest.php?cid=1045]

## A

On找一遍最长上升子区间和最长下降子区间，然后比较长度即可

```cpp
const int MAXN = 200005;
int a[MAXN];
int main()
{
    //FIN;
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n;
    cin >> n;
    for(int i = 0; i < n; i++)
    {
        cin >> a[i];
    }
    int up = 0;
    int cnt = 1;
    int flg = 0;
    for(int i  = 1; i < n; i++)
    {
        if(a[i] > a[i-1])
        {
            cnt++;
        }
        else if(a[i] < a[i-1])
        {
            up = max(cnt, up);
            cnt = 1;
        }
        else
        {
            flg = 1;
            break;
        }
        
    }
    up = max(cnt, up);
    int down = 0;
    cnt  =1;
    for(int i  = 1; i < n; i++)
    {
        if(flg) break;
        if(a[i] < a[i-1])
        {
            cnt++;
        }
        else if(a[i] > a[i-1])
        {
            down = max(cnt, down);
            cnt = 1;
        }
        else
        {
            flg = 1;
        }
        
    }
    down = max(down, cnt);
    if(flg)
    {
        cout << "inf" << endl;
    }
    else
    {
        cout << max(up, down) << endl;
    }
    
    return 0;
}

```



## B

对于每一个位置暴力往两边扩展就好，因为大写字母只有26个，每次扩展只有26次，复杂度O(26n)

```cpp
vector<int> v[26];
int vis[26];
string op;
int getans(int st)
{
    memset(vis, 0, sizeof(vis));
    int cnt = 0;
    int i = st;
    while(i < op.size())
    {
        if(vis[op[i] - 'A'] == 0)
        {
            cnt++;            
            vis[op[i] - 'A'] = 1;
            i++;
        }
        else
        {
            break;
        }  
    }
    int ret = cnt;
    return ret;
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
        
        cin >> op;
        int ans = 0;
        for(int i = 0; i < op.size(); i++)
        {
            ans = max(ans, getans(i));
        }
        cout << ans<< endl;
    }
    return 0;
}

```



## C

搜索，我们发现当一个地方可以走奇数次日字达到的时候就不可能能走偶数次日子到达，所以判断能不能奇数步到达即可

```cpp
int Map[1005][1005];
int n, m;

int dx[8] = {2,1,-1,-2,-2,-1,1,2};
int dy[8] = {-1,-2,-2,-1,1,2,2,1};

typedef struct
{
	int x, y;
	int step_cnt;
}horse;

int ex, ey;

bool check(horse a)
{
	if(a.x >= n || a.y >= m || a.x < 0 || a.y < 0) 
	{
		return false;
	}
	else if(Map[a.x][a.y] != -1) 
	{
		return false;
	}
	else 
	{
		return true;
	}
}

int bfs(horse a)
{
	queue<horse> q;
	horse now, next;
	now = a;
	now.step_cnt = 0;
    if(now.x == ex && now.y == ey)
    {
        return 0;
    }
	q.push(now);
	Map[now.x][now.y] = now.step_cnt;
	while(!q.empty())
	{
		for(int i = 0; i < 8; i++)
		{
			next.x = now.x + dx[i];
			next.y = now.y + dy[i];
			if(check(next))
			{
				next.step_cnt = now.step_cnt + 1;
				//printf("%d %d %d %d %d\n",now.x, now.y, next.x, next.y, next.step_cnt);
				q.push(next);
				Map[next.x][next.y] = next.step_cnt;
                if(next.x == ex && next.y == ey)
                {
                    return next.step_cnt;
                }
			}
		}
		q.pop();
		now = q.front();
	}
    return 0;
}

int main()
{
   // FIN;
	std::ios::sync_with_stdio(0);
	cin >> n >> m;
	horse a;
    cin >> ex >> ey;
    ex--, ey--;
    int sx, sy;
    memset(Map, -1, sizeof(Map));
    for(int i = 0; i < n; i++)
    {
        for(int j = 0; j < m; j++)
        {
            char tmp;
            cin >> tmp;
            if(tmp == '@')
            {
                sx = i, sy = j;
            }
            if(tmp == '#')
            {
                Map[i][j] = 1;
            }
        }
    }
	
	a.x = sx;
	a.y = sy;
	int flg = bfs(a);
    //cout << flg << endl;
	if(flg % 2)
    {
        cout << "yes" << endl;
    }
    else
    {
        cout << "no" << endl;
    }
    
	return 0;
}
```



## D

二分答案

先计算前缀和

check的时候枚举左端点l，看有多少个区间和大于mid，就是查看多少个r满足sum[r] - sum[l-1] > mid

可以看成sum[r] > mid + sum[l-1]

因为sum数组有单调性，所以可以二分找r

经典的二分套二分

```cpp
const ll MAXN = 1e5+5;
long long a[4*MAXN];
long long sum[4*MAXN];
ll n, k;
const long long inf = 1e18;
ll check(ll mid)
{
    ll cnt = 0;
    for(int i = 1; i <= n; i++)
    {
        ll pos = lower_bound(sum+i, sum+1+n, mid+sum[i-1]) - sum;
        cnt += n - pos + 1;
    }

    return cnt;
}

int main()
{
    cin >> n >> k;
    for(int i = 1; i <= n; i++)
    {
        cin >> a[i];
        sum[i] = sum[i-1] + a[i];
    }

    ll l = 1, r = inf;
    ll ans = 0;
    while(l <= r)
    {
        ll mid = (l + r) >> 1;
        if(check(mid) >= k)
        {
            l = mid + 1;
            ans = mid;
        }
        else
        {
            r = mid - 1;
        }
    }
    cout << ans << endl;
    return 0;
}
```



## E

根号分治/阈值优化 + 同余前缀和优化 + 滚动数组优化

详见[51nod1597 有限背包计数问题](https://blog.csdn.net/mrazer/article/details/51497933)

```cpp
const int MAXN = 1e5 + 10;
const int mod = 1e9 + 7;
int f[2][MAXN], g[320][MAXN];
ll tmp[MAXN];
int now;
ll fans[MAXN], gans[MAXN], ans;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n;
    cin >> n;
    f[0][0] = g[0][0] = 1;
    int m = ceil(sqrt(n));
    for (int i = 1; i <= m; i++)
    {
        for (int j = 0; j < i; j++)
            tmp[j] = 0;
        now ^= 1;
        for (int j = 0; j <= n; j++)
        {
            tmp[j % i] = (tmp[j % i] + f[now ^ 1][j]) % mod;
            f[now][j] = tmp[j % i];
            if (j >= i * i)
            {
                tmp[j % i] = (tmp[j % i] - f[now ^ 1][j - i * i] + mod) % mod;
            }
        }
    }

    for (int i = 0; i <= m; i++)
    {
        for (int j = 0; j <= n; j++)
        {
            if (i && j + i <= n)
            {
                g[i][j + i] = (g[i][j + i] + g[i][j]) % mod;
            }
            if (j + m + 1 <= n)
            {
                g[i + 1][j + m + 1] = (g[i + 1][j + m + 1] + g[i][j]) % mod;
            }
        }
    }

    ++gans[0];
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            gans[i] = (gans[i] + g[j][i]) % mod;
        }
    }
    for (int i = 0; i <= n; ++i)
        fans[i] = f[now][i];
    for (int i = 0; i <= n; ++i)
        (ans += (fans[i] * gans[n - i] % mod)) %= mod;

    cout << ans % mod << endl;

    return 0;
}
```



