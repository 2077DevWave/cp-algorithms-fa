---
tags:
  - AI Translated
e_maxx_link: dijkstra
---

# الگوریتم دایکسترا

یک گراف وزن‌دار جهت‌دار یا بدون جهت با $n$ رأس و $m$ یال به شما داده شده است. وزن تمام یال‌ها نامنفی است. یک رأس شروع $s$ نیز به شما داده می‌شود. این مقاله به یافتن طول کوتاه‌ترین مسیرها از رأس شروع $s$ به تمام رئوس دیگر و همچنین خودِ کوتاه‌ترین مسیرها می‌پردازد.

این مسئله با نام **مسئلهٔ کوتاه‌ترین مسیر از یک منبع واحد** نیز شناخته می‌شود.

## الگوریتم

در ادامه، الگوریتمی که توسط دانشمند کامپیوتر هلندی، ادسخر دایکسترا (Edsger W. Dijkstra) در سال ۱۹۵۹ توصیف شد، ارائه می‌شود.

بیایید آرایه‌ای به نام $d[]$ ایجاد کنیم که در آن برای هر رأس $v$، طول فعلی کوتاه‌ترین مسیر از $s$ به $v$ را در $d[v]$ ذخیره کنیم.
در ابتدا $d[s] = 0$ است و برای تمام رئوس دیگر این طول برابر با بی‌نهایت است.
در پیاده‌سازی، یک عدد به اندازه کافی بزرگ (که تضمین می‌شود از هر طول مسیر ممکنی بزرگتر باشد) به عنوان بی‌نهایت انتخاب می‌شود.

$$d[v] = \infty,~ v \ne s$$

علاوه بر این، یک آرایهٔ بولی $u[]$ نگهداری می‌کنیم که برای هر رأس $v$ مشخص می‌کند آیا آن رأس علامت‌گذاری شده است یا خیر. در ابتدا هیچ رأسی علامت‌گذاری نشده است:

$$u[v] = {\rm false}$$

الگوریتم دایکسترا برای $n$ تکرار اجرا می‌شود. در هر تکرار، یک رأس $v$ به عنوان رأس علامت‌گذاری نشده‌ای که کمترین مقدار $d[v]$ را دارد انتخاب می‌شود:

بدیهی است که در اولین تکرار، رأس شروع $s$ انتخاب خواهد شد.

رأس انتخاب‌شده $v$ علامت‌گذاری می‌شود. سپس، از رأس $v$ عملیات **ریلکسیشن (relaxation)** انجام می‌شود: تمام یال‌های به شکل $(v, \text{to})$ در نظر گرفته می‌شوند و برای هر رأس $\text{to}$، الگوریتم سعی می‌کند مقدار $d[\text{to}]$ را بهبود بخشد. اگر طول یال فعلی برابر با $len$ باشد، کد برای ریلکسیشن به این صورت است:

$$d[\text{to}] = \min (d[\text{to}], d[v] + len)$$

پس از بررسی تمام این یال‌ها، تکرار فعلی به پایان می‌رسد. در نهایت، پس از $n$ تکرار، تمام رئوس علامت‌گذاری خواهند شد و الگوریتم خاتمه می‌یابد. ما ادعا می‌کنیم که مقادیر یافت‌شده $d[v]$ طول کوتاه‌ترین مسیرها از $s$ به تمام رئوس $v$ هستند.

توجه داشته باشید که اگر برخی از رئوس از رأس شروع $s$ قابل دسترسی نباشند، مقادیر $d[v]$ برای آن‌ها بی‌نهایت باقی خواهد ماند. بدیهی است که چند تکرار آخر الگوریتم آن رئوس را انتخاب خواهند کرد، اما کار مفیدی برای آن‌ها انجام نخواهد شد. بنابراین، الگوریتم را می‌توان به محض اینکه رأس انتخاب‌شده فاصلهٔ بی‌نهایت داشته باشد، متوقف کرد.

### بازیابی کوتاه‌ترین مسیرها

معمولاً لازم است علاوه بر طول کوتاه‌ترین مسیرها، خودِ مسیرها را نیز بدانیم. بیایید ببینیم چگونه می‌توان اطلاعات کافی برای بازیابی کوتاه‌ترین مسیر از $s$ به هر رأس را نگهداری کرد. ما یک آرایه از پیشین‌ها (predecessors) به نام $p[]$ نگهداری می‌کنیم که در آن برای هر رأس $v \ne s$، $p[v]$ رأس ماقبل آخر در کوتاه‌ترین مسیر از $s$ به $v$ است. در اینجا از این واقعیت استفاده می‌کنیم که اگر کوتاه‌ترین مسیر به یک رأس $v$ را در نظر بگیریم و $v$ را از این مسیر حذف کنیم، مسیری به دست می‌آوریم که به رأس $p[v]$ ختم می‌شود و این مسیر کوتاه‌ترین مسیر برای رأس $p[v]$ خواهد بود. این آرایه از پیشین‌ها می‌تواند برای بازیابی کوتاه‌ترین مسیر به هر رأس استفاده شود: با شروع از $v$، به طور مکرر پیشینِ رأس فعلی را می‌گیریم تا به رأس شروع $s$ برسیم و کوتاه‌ترین مسیر مورد نیاز را با رئوس به ترتیب معکوس به دست آوریم. بنابراین، کوتاه‌ترین مسیر $P$ به رأس $v$ برابر است با:

$$P = (s, \ldots, p[p[p[v]]], p[p[v]], p[v], v)$$

ساختن این آرایه از پیشین‌ها بسیار ساده است: برای هر ریلکسیشن موفق، یعنی زمانی که برای یک رأس انتخاب شده $v$، بهبودی در فاصله تا یک رأس $\text{to}$ وجود دارد، ما رأس پیشین برای $\text{to}$ را با رأس $v$ به‌روزرسانی می‌کنیم:

$$p[\text{to}] = v$$

## اثبات

ادعای اصلی که صحت الگوریتم دایکسترا بر آن استوار است، به شرح زیر است:

**پس از اینکه هر رأس $v$ علامت‌گذاری شد، فاصلهٔ فعلی تا آن، یعنی $d[v]$، کوتاه‌ترین فاصله است و دیگر تغییر نخواهد کرد.**

اثبات با استقرا انجام می‌شود. برای اولین تکرار این گزاره بدیهی است: تنها رأس علامت‌گذاری شده $s$ است و فاصله تا آن $d[s] = 0$ در واقع طول کوتاه‌ترین مسیر به $s$ است. حال فرض کنید این گزاره برای تمام تکرارهای قبلی، یعنی برای تمام رئوس از قبل علامت‌گذاری شده، درست باشد؛ بیایید ثابت کنیم که پس از تکمیل تکرار فعلی، این گزاره نقض نمی‌شود. فرض کنید $v$ رأسی است که در تکرار فعلی انتخاب شده است، یعنی $v$ رأسی است که الگوریتم آن را علامت‌گذاری خواهد کرد. اکنون باید ثابت کنیم که $d[v]$ در واقع برابر با طول کوتاه‌ترین مسیر به آن، یعنی $l[v]$ است.

کوتاه‌ترین مسیر $P$ به رأس $v$ را در نظر بگیرید. این مسیر را می‌توان به دو بخش تقسیم کرد: $P_1$ که فقط از رئوس علامت‌گذاری شده تشکیل شده است (حداقل رأس شروع $s$ بخشی از $P_1$ است) و بقیه مسیر $P_2$ (ممکن است شامل یک رأس علامت‌گذاری شده باشد، اما همیشه با یک رأس علامت‌گذاری نشده شروع می‌شود). بیایید اولین رأس مسیر $P_2$ را $p$ و آخرین رأس مسیر $P_1$ را $q$ بنامیم.

ابتدا گزاره خود را برای رأس $p$ اثبات می‌کنیم، یعنی بیایید ثابت کنیم که $d[p] = l[p]$ است.
این تقریباً بدیهی است: در یکی از تکرارهای قبلی ما رأس $q$ را انتخاب کرده و از آن ریلکسیشن انجام دادیم.
از آنجا که (به دلیل انتخاب رأس $p$) کوتاه‌ترین مسیر به $p$ برابر با کوتاه‌ترین مسیر به $q$ به علاوه یال $(p, q)$ است، ریلکسیشن از $q$ مقدار $d[p]$ را برابر با طول کوتاه‌ترین مسیر $l[p]$ قرار داده است.

از آنجا که وزن یال‌ها نامنفی است، طول کوتاه‌ترین مسیر $l[p]$ (که ما ثابت کردیم برابر با $d[p]$ است) از طول $l[v]$ کوتاه‌ترین مسیر به رأس $v$ بیشتر نیست. با توجه به اینکه $l[v] \le d[v]$ (چون الگوریتم دایکسترا نمی‌توانسته راهی کوتاه‌تر از کوتاه‌ترین راه ممکن پیدا کند)، به نابرابری زیر می‌رسیم:

$$d[p] = l[p] \le l[v] \le d[v]$$

از سوی دیگر، از آنجا که هر دو رأس $p$ و $v$ علامت‌گذاری نشده‌اند و تکرار فعلی رأس $v$ را انتخاب کرده است، نه $p$، نابرابری دیگری به دست می‌آوریم:

$$d[p] \ge d[v]$$

از این دو نابرابری نتیجه می‌گیریم که $d[p] = d[v]$، و سپس از معادلات قبلی به دست می‌آوریم:

$$d[v] = l[v]$$

و حکم ثابت می‌شود.

## پیاده‌سازی

الگوریتم دایکسترا $n$ تکرار انجام می‌دهد. در هر تکرار، یک رأس علامت‌گذاری نشده $v$ با کمترین مقدار $d[v]$ را انتخاب کرده، آن را علامت‌گذاری می‌کند و تمام یال‌های $(v, \text{to})$ را بررسی می‌کند تا مقدار $d[\text{to}]$ را بهبود بخشد.

زمان اجرای الگوریتم شامل موارد زیر است:

* $n$ بار جستجو برای یافتن رأسی با کمترین مقدار $d[v]$ در میان $O(n)$ رأس علامت‌گذاری نشده.
* $m$ بار تلاش برای ریلکسیشن.

برای ساده‌ترین پیاده‌سازی این عملیات، در هر تکرار، جستجوی رأس به $O(n)$ عملیات نیاز دارد و هر ریلکسیشن می‌تواند در $O(1)$ انجام شود. از این رو، پیچیدگی مجانبی حاصل از الگوریتم به صورت زیر است:

$$O(n^2+m)$$

این پیچیدگی برای گراف‌های متراکم، یعنی زمانی که $m \approx n^2$، بهینه است.
با این حال، در گراف‌های خلوت، زمانی که $m$ بسیار کوچکتر از حداکثر تعداد یال‌ها یعنی $n^2$ است، مسئله را می‌توان با پیچیدگی $O(n \log n + m)$ حل کرد. الگوریتم و پیاده‌سازی آن را می‌توانید در مقاله [دایکسترا روی گراف‌های خلوت](dijkstra_sparse.md) بیابید.

```cpp {.cpp file=dijkstra_dense}
const int INF = 1000000000;
vector<vector<pair<int, int>>> adj;

void dijkstra(int s, vector<int> & d, vector<int> & p) {
    int n = adj.size();
    d.assign(n, INF);
    p.assign(n, -1);
    vector<bool> u(n, false);

    d[s] = 0;
    for (int i = 0; i < n; i++) {
        int v = -1;
        for (int j = 0; j < n; j++) {
            if (!u[j] && (v == -1 || d[j] < d[v]))
                v = j;
        }
        
        if (d[v] == INF)
            break;
        
        u[v] = true;
        for (auto edge : adj[v]) {
            int to = edge.first;
            int len = edge.second;
            
            if (d[v] + len < d[to]) {
                d[to] = d[v] + len;
                p[to] = v;
            }
        }
    }
}
```

در اینجا گراف $\text{adj}$ به صورت لیست مجاورت ذخیره شده است: برای هر رأس $v$، $\text{adj}[v]$ شامل لیستی از یال‌های خروجی از این رأس است، یعنی لیستی از `pair<int,int>` که عنصر اول در زوج، رأس انتهای دیگر یال و عنصر دوم، وزن یال است.

تابع، رأس شروع $s$ و دو بردار را به عنوان مقادیر بازگشتی دریافت می‌کند.

ابتدا کد آرایه‌ها را مقداردهی اولیه می‌کند: فواصل $d[]$، برچسب‌ها $u[]$ و پیشین‌ها $p[]$. سپس $n$ تکرار انجام می‌دهد. در هر تکرار، رأس $v$ که کمترین فاصله $d[v]$ را در میان تمام رئوس علامت‌گذاری نشده دارد، انتخاب می‌شود. اگر فاصله تا رأس انتخاب شده $v$ برابر با بی‌نهایت باشد، الگوریتم متوقف می‌شود. در غیر این صورت، رأس علامت‌گذاری شده و تمام یال‌های خروجی از این رأس بررسی می‌شوند. اگر ریلکسیشن در طول یال ممکن باشد (یعنی فاصله $d[\text{to}]$ بتواند بهبود یابد)، فاصله $d[\text{to}]$ و پیشین $p[\text{to}]$ به‌روزرسانی می‌شوند.

پس از انجام تمام تکرارها، آرایه $d[]$ طول کوتاه‌ترین مسیرها به تمام رئوس را ذخیره می‌کند و آرایه $p[]$ پیشین‌های تمام رئوس (به جز رأس شروع $s$) را ذخیره می‌کند. مسیر به هر رأس $t$ را می‌توان به روش زیر بازیابی کرد:

```cpp {.cpp file=dijkstra_restore_path}
vector<int> restore_path(int s, int t, vector<int> const& p) {
    vector<int> path;

    for (int v = t; v != s; v = p[v])
        path.push_back(v);
    path.push_back(s);

    reverse(path.begin(), path.end());
    return path;
}
```

## منابع

* Edsger Dijkstra. A note on two problems in connexion with graphs [1959]
* Thomas Cormen, Charles Leiserson, Ronald Rivest, Clifford Stein. Introduction to Algorithms [2005]

## مسائل تمرینی
* [Timus - Ivan's Car](http://acm.timus.ru/problem.aspx?space=1&num=1930) [سختی: متوسط]
* [Timus - Sightseeing Trip](http://acm.timus.ru/problem.aspx?space=1&num=1004)
* [SPOJ - SHPATH](http://www.spoj.com/problems/SHPATH/) [سختی: آسان]
* [Codeforces - Dijkstra?](http://codeforces.com/problemset/problem/20/C) [سختی: آسان]
* [Codeforces - Shortest Path](http://codeforces.com/problemset/problem/59/E)
* [Codeforces - Jzzhu and Cities](http://codeforces.com/problemset/problem/449/B)
* [Codeforces - The Classic Problem](http://codeforces.com/problemset/problem/464/E)
* [Codeforces - President and Roads](http://codeforces.com/problemset/problem/567/E)
* [Codeforces - Complete The Graph](http://codeforces.com/problemset/problem/715/B)
* [TopCoder - SkiResorts](https://community.topcoder.com/stat?c=problem_statement&pm=12468)
* [TopCoder - MaliciousPath](https://community.topcoder.com/stat?c=problem_statement&pm=13596)
* [SPOJ - Ada and Trip](http://www.spoj.com/problems/ADATRIP/)
* [LA - 3850 - Here We Go(relians) Again](https://vjudge.net/problem/UVALive-3850)
* [GYM - Destination Unknown (D)](http://codeforces.com/gym/100625)
* [UVA 12950 - Even Obsession](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=4829)
* [GYM - Journey to Grece (A)](http://codeforces.com/gym/100753)
* [UVA 13030 - Brain Fry](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=866&page=show_problem&problem=4918)
* [UVA 1027 - Toll](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=3468)
* [UVA 11377 - Airport Setup](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=2372)
* [Codeforces - Dynamic Shortest Path](http://codeforces.com/problemset/problem/843/D)
* [UVA 11813 - Shopping](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2913)
* [UVA 11833 - Route Change](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=226&page=show_problem&problem=2933)
* [SPOJ - Easy Dijkstra Problem](http://www.spoj.com/problems/EZDIJKST/en/)
* [LA - 2819 - Cave Raider](https://vjudge.net/problem/UVALive-2819)
* [UVA 12144 - Almost Shortest Path](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=3296)
* [UVA 12047 - Highest Paid Toll](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3198)
* [UVA 11514 - Batman](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=2509)
* [Codeforces - Team Rocket Rises Again](http://codeforces.com/contest/757/problem/F)
* [UVA - 11338 - Minefield](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2313)
* [UVA 11374 - Airport Express](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2369)
* [UVA 11097 - Poor My Problem](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2038)
* [UVA 13172 - The music teacher](https://uva.onlinejudge.org/index.php?option=onlinejudge&Itemid=8&page=show_problem&problem=5083)
* [Codeforces - Dirty Arkady's Kitchen](http://codeforces.com/contest/827/problem/F)
* [SPOJ - Delivery Route](http://www.spoj.com/problems/DELIVER/)
* [SPOJ - Costly Chess](http://www.spoj.com/problems/CCHESS/)
* [CSES - Shortest Routes 1](https://cses.fi/problemset/task/1671)
* [CSES - Flight Discount](https://cses.fi/problemset/task/1195)
* [CSES - Flight Routes](https://cses.fi/problemset/task/1196)