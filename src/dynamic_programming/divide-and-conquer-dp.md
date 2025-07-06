---
tags:
  - AI Translated
e_maxx_link: divide-and-conquer-dp
---

# DP تقسیم و حل

تقسیم و حل یک بهینه‌سازی برای برنامه‌نویسی پویا است.

### پیش‌شرط‌ها
بعضی از مسائل برنامه‌نویسی پویا یک رابطه‌ی بازگشتی به این شکل دارند:

$$
dp(i, j) = \min_{0 \leq k \leq j} \\{ dp(i - 1, k - 1) + C(k, j) \\}
$$

که در آن $C(k, j)$ یک تابع هزینه است و $dp(i, j) = 0$ وقتی $j \lt 0$ باشد.

فرض کنید $0 \leq i \lt m$ و $0 \leq j \lt n$ و محاسبه‌ی $C$ زمان $O(1)$ می‌برد. در این صورت، محاسبه‌ی مستقیم رابطه‌ی بازگشتی بالا $O(m n^2)$ خواهد بود. تعداد $m \times n$ حالت وجود دارد و برای هر حالت، $n$ انتقال داریم.

فرض کنید $opt(i, j)$ مقداری از $k$ باشد که عبارت بالا را کمینه می‌کند. با فرض اینکه تابع هزینه در نامساوی چهارضلعی صدق کند، می‌توانیم نشان دهیم که به ازای تمام $i$ و $j$ ها، $opt(i, j) \leq opt(i, j + 1)$ برقرار است. این شرط به عنوان _شرط یکنوایی_ (monotonicity condition) شناخته می‌شود. در این صورت، می‌توانیم از DP تقسیم و حل استفاده کنیم. نقطه‌ی شکست بهینه برای یک $i$ ثابت، با افزایش $j$ افزایش می‌یابد.

این ویژگی به ما اجازه می‌دهد تا تمام حالت‌ها را به شکل بهینه‌تری حل کنیم. فرض کنید $opt(i, j)$ را برای یک $i$ و $j$ ثابت محاسبه می‌کنیم. در این صورت برای هر $j' < j$ می‌دانیم که $opt(i, j') \leq opt(i, j)$. این یعنی هنگام محاسبه‌ی $opt(i, j')$، نیازی نیست تعداد زیادی نقطه‌ی شکست را بررسی کنیم!

برای کمینه کردن زمان اجرا، از ایده‌ی پشت تقسیم و حل استفاده می‌کنیم. ابتدا $opt(i, n / 2)$ را محاسبه می‌کنیم. سپس، $opt(i, n / 4)$ را با دانستن اینکه از $opt(i, n / 2)$ کوچکتر یا مساوی است و $opt(i, 3 n / 4)$ را با دانستن اینکه از $opt(i, n / 2)$ بزرگتر یا مساوی است، محاسبه می‌کنیم. با پیگیری بازگشتی کران‌های بالا و پایین برای $opt$، به زمان اجرای $O(m n \log n)$ می‌رسیم. هر مقدار ممکن برای $opt(i, j)$ تنها در $\log n$ گره متفاوت ظاهر می‌شود.

توجه داشته باشید که مهم نیست $opt(i, j)$ چقدر «متوازن» باشد. در یک سطح ثابت، هر مقدار $k$ حداکثر دو بار استفاده می‌شود، و حداکثر $\log n$ سطح وجود دارد.

## پیاده‌سازی کلی

اگرچه پیاده‌سازی بسته به مسئله متفاوت است، در اینجا یک قالب نسبتاً کلی ارائه شده است.
تابع `compute` یک سطر $i$ از حالت‌های `dp_cur` را با داشتن سطر قبلی $i-1$ یعنی `dp_before` محاسبه می‌کند.
این تابع باید با `compute(0, n-1, 0, n-1)` فراخوانی شود. تابع `solve` تعداد `m` سطر را محاسبه کرده و نتیجه را برمی‌گرداند.

```{.cpp file=divide_and_conquer_dp}
int m, n;
vector<long long> dp_before, dp_cur;

long long C(int i, int j);

// محاسبه‌ی dp_cur[l] تا dp_cur[r] (شامل خودشان)
void compute(int l, int r, int optl, int optr) {
    if (l > r)
        return;

    int mid = (l + r) >> 1;
    pair<long long, int> best = {LLONG_MAX, -1};

    for (int k = optl; k <= min(mid, optr); k++) {
        best = min(best, {(k ? dp_before[k - 1] : 0) + C(k, mid), k});
    }

    dp_cur[mid] = best.first;
    int opt = best.second;

    compute(l, mid - 1, optl, opt);
    compute(mid + 1, r, opt, optr);
}

long long solve() {
    dp_before.assign(n,0);
    dp_cur.assign(n,0);

    for (int i = 0; i < n; i++)
        dp_before[i] = C(0, i);

    for (int i = 1; i < m; i++) {
        compute(0, n - 1, 0, n - 1);
        dp_before = dp_cur;
    }

    return dp_before[n - 1];
}
```

### نکات قابل توجه

بزرگترین چالش در مسائل DP تقسیم و حل، اثبات یکنوایی (monotonicity) $opt$ است. یک حالت خاص که این شرط در آن برقرار است، زمانی است که تابع هزینه در نامساوی چهارضلعی صدق کند، یعنی $C(a, c) + C(b, d) \leq C(a, d) + C(b, c)$ به ازای تمام $a \leq b \leq c \leq d$.
بسیاری از مسائل DP تقسیم و حل را می‌توان با ترفند «پوسته‌ی محدب» (Convex Hull trick) نیز حل کرد و بالعکس. دانستن و درک هر دو روش مفید است!

## مسائل تمرینی
- [AtCoder - Yakiniku Restaurants](https://atcoder.jp/contests/arc067/tasks/arc067_d)
- [CodeForces - Ciel and Gondolas](https://codeforces.com/contest/321/problem/E) (مراقب ورودی/خروجی باشید!)
- [CodeForces - Levels And Regions](https://codeforces.com/problemset/problem/673/E)
- [CodeForces - Partition Game](https://codeforces.com/contest/1527/problem/E)
- [CodeForces - The Bakery](https://codeforces.com/problemset/problem/834/D)
- [CodeForces - Yet Another Minimization Problem](https://codeforces.com/contest/868/problem/F)
- [Codechef - CHEFAOR](https://www.codechef.com/problems/CHEFAOR)
- [CodeForces - GUARDS](https://codeforces.com/gym/103536/problem/A) (این دقیقاً مسئله‌ی توضیح داده شده در این مقاله است.)
- [Hackerrank - Guardians of the Lunatics](https://www.hackerrank.com/contests/ioi-2014-practice-contest-2/challenges/guardians-lunatics-ioi14)
- [Hackerrank - Mining](https://www.hackerrank.com/contests/world-codesprint-5/challenges/mining)
- [Kattis - Money (ACM ICPC World Finals 2017)](https://open.kattis.com/problems/money)
- [SPOJ - ADAMOLD](https://www.spoj.com/problems/ADAMOLD/)
- [SPOJ - LARMY](https://www.spoj.com/problems/LARMY/)
- [SPOJ - NKLEAVES](https://www.spoj.com/problems/NKLEAVES/)
- [Timus - Bicolored Horses](https://acm.timus.ru/problem.aspx?space=1&num=1167)
- [USACO - Circular Barn](https://usaco.org/index.php?page=viewproblem2&cpid=626)
- [UVA - Arranging Heaps](https://onlinejudge.org/external/125/12524.pdf)
- [UVA - Naming Babies](https://onlinejudge.org/external/125/12594.pdf)



## منابع
- [Quora Answer by Michael Levin](https://www.quora.com/What-is-divide-and-conquer-optimization-in-dynamic-programming)
- [Video Tutorial by "Sothe" the Algorithm Wolf](https://www.youtube.com/watch?v=wLXEWuDWnzI)