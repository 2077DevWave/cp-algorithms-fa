---
tags:
  - AI Translated
e_maxx_link: 01_bfs
---

# 0-1 BFS

همان‌طور که می‌دانید، می‌توان کوتاه‌ترین مسیرها بین یک رأس منبع و تمام رئوس دیگر را در یک **گراف بدون وزن** با استفاده از [جستجوی اول سطح (BFS)](breadth-first-search.md) در زمان $O(|E|)$ پیدا کرد. به عبارت دیگر، فاصله برابر است با حداقل تعداد یال‌هایی که برای رفتن از منبع به یک رأس دیگر باید طی کرد.
می‌توان چنین گرافی را به عنوان یک گراف وزن‌دار نیز در نظر گرفت که در آن وزن هر یال برابر با $1$ است.
اگر تمام یال‌های گراف وزن یکسانی نداشته باشند، به الگوریتم کلی‌تری مانند [دایجسترا](dijkstra.md) نیاز داریم که در زمان $O(|V|^2 + |E|)$ یا $O(|E| \log |V|)$ اجرا می‌شود.

اما اگر وزن‌ها محدودیت‌های بیشتری داشته باشند، اغلب می‌توانیم عملکرد بهتری داشته باشیم.
در این مقاله نشان می‌دهیم که چگونه می‌توان با استفاده از BFS، مسئله کوتاه‌ترین مسیر از یک منبع واحد (SSSP) را در زمان $O(|E|)$ حل کرد، به شرطی که وزن هر یال یا $0$ یا $1$ باشد.

## الگوریتم

می‌توانیم با مطالعه دقیق الگوریتم دایجسترا و فکر کردن به پیامدهایی که گراف خاص ما به همراه دارد، این الگوریتم را توسعه دهیم.
شکل کلی الگوریتم دایجسترا به صورت زیر است (در اینجا از یک `set` به عنوان صف اولویت استفاده شده است):

```cpp
d.assign(n, INF);
d[s] = 0;
set<pair<int, int>> q;
q.insert({0, s});
while (!q.empty()) {
    int v = q.begin()->second;
    q.erase(q.begin());

    for (auto edge : adj[v]) {
        int u = edge.first;
        int w = edge.second;

        if (d[v] + w < d[u]) {
            q.erase({d[u], u});
            d[u] = d[v] + w;
            q.insert({d[u], u});
        }
    }
}
```

می‌توانیم متوجه شویم که فاصله‌های رئوس موجود در صف از مبدأ `s`، حداکثر یک واحد با هم اختلاف دارند.
به‌طور خاص، می‌دانیم که برای هر $u \in Q$، رابطه $d[v] \le d[u] \le d[v] + 1$ برقرار است.
دلیل این امر این است که ما در هر تکرار، تنها رئوسی با فاصله برابر یا فاصله به‌علاوه یک را به صف اضافه می‌کنیم.
با فرض وجود رأسی مانند `u` در صف که $d[u] - d[v] > 1$ باشد، آنگاه `u` باید از طریق رأس دیگری مانند `t` با $d[t] \ge d[u] - 1 > d[v]$ به صف اضافه شده باشد.
اما این غیرممکن است، زیرا الگوریتم دایجسترا رئوس را به ترتیب صعودی فاصله پردازش می‌کند.

این بدان معناست که ترتیب صف به این شکل خواهد بود:

$$Q = \underbrace{v}_{d[v]}, \dots, \underbrace{u}_{d[v]}, \underbrace{m}_{d[v]+1} \dots \underbrace{n}_{d[v]+1}$$

این ساختار آنقدر ساده است که نیازی به یک صف اولویت واقعی نداریم؛ یعنی استفاده از یک درخت دودویی متوازن زیاده‌روی است.
می‌توانیم به سادگی از یک صف معمولی (دک) استفاده کنیم و رئوس جدید را اگر یال متناظر وزن $0$ داشته باشد (یعنی $d[u] = d[v]$) به ابتدای صف، و اگر وزن $1$ داشته باشد (یعنی $d[u] = d[v] + 1$) به انتهای صف اضافه کنیم.
به این ترتیب، صف همیشه مرتب باقی می‌ماند.

```cpp
vector<int> d(n, INF);
d[s] = 0;
deque<int> q;
q.push_front(s);
while (!q.empty()) {
    int v = q.front();
    q.pop_front();
    for (auto edge : adj[v]) {
        int u = edge.first;
        int w = edge.second;
        if (d[v] + w < d[u]) {
            d[u] = d[v] + w;
            if (w == 1)
                q.push_back(u);
            else
                q.push_front(u);
        }
    }
}
```

## الگوریتم دایل

می‌توانیم این ایده را حتی فراتر ببریم و اجازه دهیم وزن یال‌ها مقادیر بزرگتری داشته باشند.
اگر وزن هر یال در گراف حداکثر $k$ باشد، آنگاه اختلاف فاصله رئوس موجود در صف با فاصله رأس `v` از منبع، حداکثر $k$ خواهد بود.
بنابراین می‌توانیم $k + 1$ باکت برای رئوس موجود در صف نگه داریم و هرگاه باکتی که مربوط به کمترین فاصله است خالی شد، یک شیفت چرخشی انجام می‌دهیم تا به باکت با فاصله بالاتر بعدی برویم.
این تعمیم، **الگوریتم دایل** (Dial's algorithm) نامیده می‌شود.

## مسائل تمرینی

- [CodeChef - Chef and Reversing](https://www.codechef.com/problems/REVERSE)
- [Labyrinth](https://codeforces.com/contest/1063/problem/B)
- [KATHTHI](http://www.spoj.com/problems/KATHTHI/)
- [DoNotTurn](https://community.topcoder.com/stat?c=problem_statement&pm=10337)
- [Ocean Currents](https://onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=2620)
- [Olya and Energy Drinks](https://codeforces.com/problemset/problem/877/D)
- [Three States](https://codeforces.com/problemset/problem/590/C)
- [Colliding Traffic](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2621)
- [CHamber of Secrets](https://codeforces.com/problemset/problem/173/B)
- [Spiral Maximum](https://codeforces.com/problemset/problem/173/C)
- [Minimum Cost to Make at Least One Valid Path in a Grid](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid)