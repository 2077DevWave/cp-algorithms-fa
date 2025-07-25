---
tags:
  - AI Translated
e_maxx_link: finding-negative-cycle-in-graph
---

# پیدا کردن دور منفی در گراف

یک گراف جهت‌دار و وزن‌دار $G$ با $N$ رأس و $M$ یال به شما داده شده است. اگر در این گراف دوری با وزن منفی وجود دارد، یک نمونه از آن را پیدا کنید.

در صورت‌بندی دیگری از این مسئله، شما باید تمام زوج رأس‌هایی را پیدا کنید که بین آن‌ها مسیری با وزن دلخواه کوچک وجود دارد.

برای حل این دو نوع از مسئله، استفاده از الگوریتم‌های مختلفی مناسب است، بنابراین هر دو را در اینجا بررسی خواهیم کرد.

## استفاده از الگوریتم Bellman-Ford

الگوریتم Bellman-Ford به شما امکان می‌دهد بررسی کنید که آیا دوری با وزن منفی در گراف وجود دارد یا نه، و در صورت وجود، یکی از این دورها را پیدا کنید.

جزئیات این الگوریتم در مقاله [الگوریتم Bellman-Ford](bellman_ford.md) توضیح داده شده است. در اینجا فقط به کاربرد آن در این مسئله می‌پردازیم.

پیاده‌سازی استاندارد Bellman-Ford به دنبال یک دور منفی قابل دسترس از یک رأس شروع $v$ می‌گردد؛ با این حال، می‌توان الگوریتم را طوری تغییر داد که به دنبال هر دور منفی در گراف باشد. برای این کار، باید تمام فاصله‌ها، یعنی $d[i]$، را به جای بی‌نهایت، برابر با صفر قرار دهیم — گویی که به طور همزمان به دنبال کوتاه‌ترین مسیر از تمام رأس‌ها هستیم؛ این کار بر صحت تشخیص دور منفی تأثیری نمی‌گذارد.

الگوریتم Bellman-Ford را به تعداد $N$ تکرار اجرا کنید. اگر در تکرار آخر هیچ تغییری رخ نداد، هیچ دور با وزن منفی در گراف وجود ندارد. در غیر این صورت، رأسی را که فاصله‌اش تغییر کرده است بردارید و از طریق والدین آن به عقب برگردید تا یک دور پیدا شود. این دور، همان دور با وزن منفی مورد نظر خواهد بود.

### پیاده‌سازی

```cpp
struct Edge {
    int a, b, cost;
};
 
int n;
vector<Edge> edges;
const int INF = 1000000000;
 
void solve() {
    vector<int> d(n, 0);
    vector<int> p(n, -1);
    int x;
 
    for (int i = 0; i < n; ++i) {
        x = -1;
        for (Edge e : edges) {
            if (d[e.a] + e.cost < d[e.b]) {
                d[e.b] = max(-INF, d[e.a] + e.cost);
                p[e.b] = e.a;
                x = e.b;
            }
        }
    }
 
    if (x == -1) {
        cout << "هیچ دور منفی پیدا نشد.";
    } else {
        for (int i = 0; i < n; ++i)
            x = p[x];
 
        vector<int> cycle;
        for (int v = x;; v = p[v]) {
            cycle.push_back(v);
            if (v == x && cycle.size() > 1)
                break;
        }
        reverse(cycle.begin(), cycle.end());
 
        cout << "دور منفی: ";
        for (int v : cycle)
            cout << v << ' ';
        cout << endl;
    }
}
```

## استفاده از الگوریتم Floyd-Warshall

الگوریتم Floyd-Warshall امکان حل نوع دوم مسئله را فراهم می‌کند - یعنی پیدا کردن تمام زوج رأس‌های $(i, j)$ که کوتاه‌ترین مسیر بین آن‌ها وجود ندارد (یعنی مسیری با وزن دلخواه کوچک وجود دارد).

باز هم، جزئیات را می‌توان در مقاله [الگوریتم Floyd-Warshall](all-pair-shortest-path-floyd-warshall.md) یافت و ما در اینجا فقط کاربرد آن را شرح می‌دهیم.

الگوریتم Floyd-Warshall را روی گراف اجرا کنید. در ابتدا برای هر رأس $v$، مقدار $d[v][v] = 0$ است. اما پس از اجرای الگوریتم، اگر یک مسیر با طول منفی از $v$ به خودش وجود داشته باشد، مقدار $d[v][v]$ کمتر از $0$ خواهد شد. ما می‌توانیم از این ویژگی برای پیدا کردن تمام زوج رأس‌هایی که کوتاه‌ترین مسیر بینشان وجود ندارد نیز استفاده کنیم. روی تمام زوج رأس‌های $(i, j)$ تکرار می‌کنیم و برای هر زوج بررسی می‌کنیم که آیا کوتاه‌ترین مسیر بین آن‌ها وجود دارد یا خیر. برای این کار، تمام رأس‌های میانی ممکن $t$ را امتحان می‌کنیم. زوج $(i, j)$ کوتاه‌ترین مسیر ندارد، اگر یکی از رأس‌های میانی $t$ دارای $d[t][t] < 0$ باشد (یعنی $t$ بخشی از یک دور با وزن منفی است)، $t$ از $i$ قابل دسترس باشد و $j$ از $t$ قابل دسترس باشد. در این صورت، مسیر از $i$ به $j$ می‌تواند وزن دلخواه کوچکی داشته باشد. ما این حالت را با `-INF` نشان خواهیم داد.

### پیاده‌سازی

```cpp
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < n; ++j) {
        for (int t = 0; t < n; ++t) {
            if (d[i][t] < INF && d[t][t] < 0 && d[t][j] < INF)
                d[i][j] = - INF; 
        }
    }
}
```

## مسائل تمرینی

- [UVA: Wormholes](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=499)
- [SPOJ: Alice in Amsterdam, I mean Wonderland](http://www.spoj.com/problems/UCV2013B/)
- [SPOJ: Johnsons Algorithm](http://www.spoj.com/problems/JHNSN/)