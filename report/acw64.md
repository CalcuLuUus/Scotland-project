# [ACW第64周赛](https://www.acwing.com/activity/content/competition/problem_list/2157/)

---

### C [4507. 子数组异或和 - AcWing题库](https://www.acwing.com/problem/content/4510/)

结论：对于任何一个异或和为0的序列，无论其间断点在哪里，**间断点左右子区间的异或值都相等**（易得，左右区间异或值相等，异或起来就为0）

```cpp
const int MAXN = 3e5+5;
int a[MAXN];
int xorsum[MAXN];

vector<int> v[(1 << 20) + 5][2];

int getxor(int l, int r)
{
    return xorsum[r] ^ xorsum[l-1];
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
        xorsum[i] = (xorsum[i-1] ^ a[i]);
    }
    
    v[0][0].push_back(0);
    ll ans = 0;
    for(int i = 1; i <= n; i++)
    {
        int num = xorsum[i];
        ans += v[num][i % 2].size();
        v[num][i % 2].push_back(i);
    }
    cout << ans << endl;
    return 0;
}
```



### D [4508. 移动的点 - AcWing题库](https://www.acwing.com/problem/content/4511/)

![](https://raw.githubusercontent.com/CalcuLuUus/pics/main/1832651037390C4A851CB73FFC2C3B11.png)

```cpp
map<ll, int> mp;
map<P, int> cnt;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int n, a, b;
    cin >> n >> a >> b;

    ll ans = 0;
    for(int i = 0; i < n; i++)
    {
        ll x, vx, vy;
        cin >> x >> vx >> vy;
        ll tmp = vy - a * vx;
        ans += mp[tmp] - cnt[{vx, vy}];
        mp[tmp]++;
        cnt[{vx, vy}]++;
    }
    cout << ans*2 << endl;
    return 0;
}
```


