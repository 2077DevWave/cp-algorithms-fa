---
tags:
  - AI Translated
e_maxx_link: sparse-table
---

# Sparse Table

Sparse Table یک ساختمان داده است که امکان پاسخ‌گویی به پرس‌وجوهای بازه‌ای (range queries) را فراهم می‌کند.
این ساختمان داده می‌تواند به اکثر پرس‌وجوهای بازه‌ای در زمان $O(\log n)$ پاسخ دهد، اما قدرت اصلی آن در پاسخ‌گویی به پرس‌وجوهای کمینه بازه‌ای (range minimum queries) (یا معادل آن، پرس‌وجوهای بیشینه بازه‌ای) است.
برای این نوع پرس‌وجوها، می‌تواند پاسخ را در زمان $O(1)$ محاسبه کند.

تنها نقطه ضعف این ساختمان داده این است که فقط می‌توان از آن روی آرایه‌های _تغییرناپذیر_ (immutable) استفاده کرد.
این یعنی آرایه بین دو پرس‌وجو نباید تغییر کند.
اگر هر عنصری در آرایه تغییر کند، کل ساختمان داده باید از نو محاسبه شود.

## شهود

هر عدد نامنفی را می‌توان به طور منحصربه‌فرد به صورت مجموعی از توان‌های کاهشی از دو نمایش داد.
این در واقع شکلی دیگر از نمایش دودویی یک عدد است.
مثلاً $13 = (1101)_2 = 8 + 4 + 1$.
برای یک عدد $x$، حداکثر $\lceil \log_2 x \rceil$ جمله در این مجموع وجود خواهد داشت.

با همین استدلال، هر بازه را می‌توان به طور منحصربه‌فرد به صورت اجتماعی از بازه‌هایی با طول‌های توانی از دو (به صورت کاهشی) نمایش داد.
برای مثال، بازه $[2, 14]$ را می‌توان به صورت $[2, 9] \cup [10, 13] \cup [14, 14]$ نوشت، که در آن طول بازه کامل ۱۳ و طول بازه‌های مجزا به ترتیب ۸، ۴ و ۱ است.
در اینجا نیز، این اجتماع حداکثر از $\lceil \log_2(\text{طول بازه}) \rceil$ بازه تشکیل شده است.

ایده اصلی پشت Sparse Table، پیش‌محاسبه کردن پاسخ تمام پرس‌وجوهای بازه‌ای است که طول آن‌ها توانی از دو است.
پس از آن، می‌توان به یک پرس‌وجوی بازه‌ای دلخواه، با شکستن آن بازه به بازه‌هایی با طول توانی از دو، پیدا کردن پاسخ‌های از پیش محاسبه‌شده و ترکیب آن‌ها برای رسیدن به جواب نهایی، پاسخ داد.

## پیش‌محاسبه

ما از یک آرایه دو بعدی برای ذخیره پاسخ‌های پرس‌وجوهای پیش‌محاسبه‌شده استفاده خواهیم کرد.
$\text{st}[i][j]$ پاسخ برای بازه $[j, j + 2^i - 1]$ به طول $2^i$ را ذخیره می‌کند.
اندازه آرایه دو بعدی $(K + 1) \times \text{MAXN}$ خواهد بود، که در آن $\text{MAXN}$ بزرگ‌ترین طول ممکن برای آرایه است.
مقدار $\text{K}$ باید در شرط $\text{K} \ge \lfloor \log_2 \text{MAXN} \rfloor$ صدق کند، زیرا $2^{\lfloor \log_2 \text{MAXN} \rfloor}$ بزرگ‌ترین بازه با طول توانی از دو است که باید پشتیبانی کنیم.
برای آرایه‌هایی با طول معقول (تا $10^7$ عنصر)، $K = 25$ مقدار مناسبی است.

بعدِ $\text{MAXN}$ به عنوان بعد دوم قرار داده شده تا دسترسی‌های متوالی به حافظه (سازگار با حافظه پنهان) امکان‌پذیر باشد.

```cpp
int st[K + 1][MAXN];
```

از آنجا که بازه $[j, j + 2^i - 1]$ به طول $2^i$ به خوبی به دو بازه $[j, j + 2^{i-1} - 1]$ و $[j + 2^{i-1}, j + 2^i - 1]$، که هر دو طولی برابر با $2^{i-1}$ دارند، تقسیم می‌شود، می‌توانیم جدول را با استفاده از برنامه‌ریزی پویا (dynamic programming) به طور بهینه بسازیم:

```cpp
std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = f(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

تابع $f$ به نوع پرس‌وجو بستگی دارد.
برای پرس‌وجوهای مجموع بازه‌ای، این تابع مجموع را محاسبه می‌کند و برای پرس‌وجوهای کمینه بازه‌ای، کمینه را محاسبه می‌کند.

پیچیدگی زمانی پیش‌محاسبه $O(\text{N} \log \text{N})$ است.

## پرس‌وجوهای مجموع بازه‌ای

برای این نوع پرس‌وجوها، می‌خواهیم مجموع تمام مقادیر در یک بازه را پیدا کنیم.
بنابراین، تعریف طبیعی برای تابع $f$ به صورت $f(x, y) = x + y$ است.
می‌توانیم ساختمان داده را به این صورت بسازیم:

```cpp
long long st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = st[i - 1][j] + st[i - 1][j + (1 << (i - 1))];
```

برای پاسخ به پرس‌وجوی مجموع برای بازه $[L, R]$، ما روی تمام توان‌های دو، از بزرگ‌ترین آن‌ها شروع کرده و پیمایش می‌کنیم.
هرگاه توان دویی مانند $2^i$ کوچک‌تر یا مساوی طول بازه ($= R - L + 1$) باشد، بخش اول بازه یعنی $[L, L + 2^i - 1]$ را پردازش کرده و با بازه باقیمانده یعنی $[L + 2^i, R]$ ادامه می‌دهیم.

```cpp
long long sum = 0;
for (int i = K; i >= 0; i--) {
    if ((1 << i) <= R - L + 1) {
        sum += st[i][L];
        L += 1 << i;
    }
}
```

پیچیدگی زمانی برای یک پرس‌وجوی مجموع بازه‌ای $O(K) = O(\log \text{MAXN})$ است.

## پرس‌وجوهای کمینه بازه‌ای (RMQ)

این‌ها پرس‌وجوهایی هستند که Sparse Table در آن‌ها می‌درخشد.
هنگام محاسبه کمینه یک بازه، مهم نیست که یک مقدار را یک بار یا دو بار پردازش کنیم.
بنابراین، به جای تقسیم یک بازه به چندین بازه، می‌توانیم آن را فقط به دو بازه همپوشان با طول توانی از دو تقسیم کنیم.
مثلاً، می‌توانیم بازه $[1, 6]$ را به بازه‌های $[1, 4]$ و $[3, 6]$ تقسیم کنیم.
کمینه بازه $[1, 6]$ به وضوح با کمینهِ «کمینه بازه $[1, 4]$» و «کمینه بازه $[3, 6]$» برابر است.
پس می‌توانیم کمینه بازه $[L, R]$ را با فرمول زیر محاسبه کنیم:

$$\min(\text{st}[i][L], \text{st}[i][R - 2^i + 1]) \quad \text{ که در آن } i = \log_2(R - L + 1)$$

این کار مستلزم آن است که بتوانیم $\log_2(R - L + 1)$ را سریع محاسبه کنیم.
می‌توانید این کار را با پیش‌محاسبه تمام لگاریتم‌ها انجام دهید:

```cpp
int lg[MAXN+1];
lg[1] = 0;
for (int i = 2; i <= MAXN; i++)
    lg[i] = lg[i/2] + 1;
```
به طور جایگزین، لگاریتم را می‌توان به صورت درلحظه (on the fly) با فضا و زمان ثابت محاسبه کرد:
```cpp
// C++20
#include <bit>
int log2_floor(unsigned long i) {
    return std::bit_width(i) - 1;
}

// pre C++20
int log2_floor(unsigned long long i) {
    return i ? __builtin_clzll(1) - __builtin_clzll(i) : -1;
}
```
[این بنچمارک](https://quick-bench.com/q/Zghbdj_TEkmw4XG2nqOpD3tsJ8U) نشان می‌دهد که استفاده از آرایه `lg` به دلیل خطاهای حافظه پنهان (cache misses) کندتر است.

سپس باید ساختار Sparse Table را پیش‌محاسبه کنیم. این بار $f$ را با $f(x, y) = \min(x, y)$ تعریف می‌کنیم.

```cpp
int st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = min(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

و کمینه بازه $[L, R]$ را می‌توان به این صورت محاسبه کرد:

```cpp
int i = lg[R - L + 1];
int minimum = min(st[i][L], st[i][R - (1 << i) + 1]);
```

پیچیدگی زمانی برای یک پرس‌وجوی کمینه بازه‌ای $O(1)$ است.

## ساختمان داده‌های مشابه با پشتیبانی از انواع بیشتری از پرس‌وجوها

یکی از ضعف‌های اصلی رویکرد $O(1)$ که در بخش قبل بحث شد این است که این رویکرد تنها از پرس‌وجوهایی با [توابع خودتوان (idempotent)](https://en.wikipedia.org/wiki/Idempotence) پشتیبانی می‌کند.
یعنی برای پرس‌وجوهای کمینه بازه‌ای عالی عمل می‌کند، اما پاسخ به پرس‌وجوهای مجموع بازه‌ای با این رویکرد ممکن نیست.

ساختمان داده‌های مشابهی وجود دارند که می‌توانند هر نوع تابع شرکت‌پذیری را مدیریت کرده و به پرس‌وجوهای بازه‌ای در زمان $O(1)$ پاسخ دهند.
یکی از آن‌ها [Disjoint Sparse Table](https://discuss.codechef.com/questions/117696/tutorial-disjoint-sparse-table) نام دارد.
مورد دیگر [Sqrt Tree](sqrt-tree.md) است.

## مسائل تمرینی

* [SPOJ - RMQSQ](http://www.spoj.com/problems/RMQSQ/)
* [SPOJ - THRBL](http://www.spoj.com/problems/THRBL/)
* [Codechef - MSTICK](https://www.codechef.com/problems/MSTICK)
* [Codechef - SEAD](https://www.codechef.com/problems/SEAD)
* [Codeforces - CGCDSSQ](http://codeforces.com/contest/475/problem/D)
* [Codeforces - R2D2 and Droid Army](http://codeforces.com/problemset/problem/514/D)
* [Codeforces - Maximum of Maximums of Minimums](http://codeforces.com/problemset/problem/872/B)
* [SPOJ - Miraculous](http://www.spoj.com/problems/TNVFC1M/)
* [DevSkill - Multiplication Interval (archived)](http://web.archive.org/web/20200922003506/https://devskill.com/CodingProblems/ViewProblem/19)
* [Codeforces - Animals and Puzzles](http://codeforces.com/contest/713/problem/D)
* [Codeforces - Trains and Statistics](http://codeforces.com/contest/675/problem/E)
* [SPOJ - Postering](http://www.spoj.com/problems/POSTERIN/)
* [SPOJ - Negative Score](http://www.spoj.com/problems/RPLN/)
* [SPOJ - A Famous City](http://www.spoj.com/problems/CITY2/)
* [SPOJ - Diferencija](http://www.spoj.com/problems/DIFERENC/)
* [Codeforces - Turn off the TV](http://codeforces.com/contest/863/problem/E)
* [Codeforces - Map](http://codeforces.com/contest/15/problem/D)
* [Codeforces - Awards for Contestants](http://codeforces.com/contest/873/problem/E)
* [Codeforces - Longest Regular Bracket Sequence](http://codeforces.com/contest/5/problem/C)
* [Codeforces - Array Stabilization (GCD version)](http://codeforces.com/problemset/problem/1547/F)