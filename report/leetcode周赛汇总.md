# 309周竞赛

---

## T2

组合数出错过多

## T4

给你一个整数 n ，共有编号从 0 到 n - 1 的 n 个会议室。

给你一个二维整数数组 meetings ，其中 meetings[i] = [starti, endi] 表示一场会议将会在 半闭 时间区间 [starti, endi) 举办。所有 starti 的值 互不相同 。

会议将会按以下方式分配给会议室：

1. 每场会议都会在未占用且编号 最小 的会议室举办。

2. 如果没有可用的会议室，会议将会延期，直到存在空闲的会议室。延期会议的持续时间和原会议持续时间 相同 。

3. 当会议室处于未占用状态时，将会优先提供给原 开始 时间更早的会议。

返回举办最多次会议的房间 编号 。如果存在多个房间满足此条件，则返回编号 最小 的房间。

半闭区间 [a, b) 是 a 和 b 之间的区间，包括 a 但 不包括 b 。



### 思路

模拟

优先队列记录<结束时间，占用会议室>, 一个记录<空会议室>，模拟

```cpp
typedef long long ll;
typedef pair<ll, ll> P;
class Solution {
public:
    int mostBooked(int n, vector<vector<int>>& meetings) {
        int m = meetings.size();
        vector<vector<ll>> meeting(m, vector<ll>(2, 0));
        for(int i = 0; i < m; i++)
        {
            for(int j = 0; j < 2; j++)
            {
                meeting[i][j] = meetings[i][j];
            }
        }
        

        sort(meeting.begin(), meeting.end(), [](vector<ll> &a, vector<ll>&b){
            return a[0] < b[0];
        });
        
        
        priority_queue<P, vector<P>, greater<P>> tim;
        priority_queue<int, vector<int>, greater<int>> emp;
        vector<int> cnt(n, 0);
        for(int i = 0; i < n; i++)
        {
            emp.push(i);
        }
        ll offset = 0;
        ll ST = 0;
        
        for(int i = 0; i < m; i++)
        {
            ll &st = meeting[i][0], &ed = meeting[i][1];
            ST = max(ST, st);
            if(st < ST)
            {
                ed += ST - st;
                st = ST;
            }
            while(tim.size() && tim.top().first <= st)
            {
                auto tmp = tim.top();
                tim.pop();
                int id = tmp.second;
                emp.push(id);
            }
            
            if(emp.size())
            {
                int id = emp.top();
                emp.pop();
                cnt[id]++;
                tim.push({ed, id});
            }
            else
            {
                offset = tim.top().first - st;
                meeting[i][0] += offset, meeting[i][1] += offset;
                i--;
            }
        }
        int ans = -1, pos = -1;
        for(int i = 0; i < n; i++)
        {
            if(ans < cnt[i])
            {
                ans = cnt[i];
                pos = i;
            }
        }
        return pos;
    }
};
```

1. 赛中的时候set的写法没有考虑到中途会有空会议室，set二分会直接找最早结束的会议室，但是有可能在那个时间点早就有会议室空余了, 比如set里面有{12, 0}{13， 1}如果按照结束时间二分的话会替代{13, 1}，但是会议室0是更好的选择
2. 赛后补题的时候用了offset，但是只有在算没有空会议室的时候对当前的时间加上offset但是没有考虑到后面的会议时间，比如目前有{2, 7}{3， 7}在开会，后面是{4， 5}{5， 6}，我的思路只有把{4, 5}变成{7， 8}，但是没处理{5， 6}，这样的问题就是上了{7， 8}之后如果有空的会议室{5， 6}也会直接入堆，但是这样结束时间就不对了，因为如果{4, 5}变成{7， 8}，{5， 6}就会变成{7， 8}，但是暴力往后维护会超时，这时候维护一个最晚开始时间，然后后续的开始时间


