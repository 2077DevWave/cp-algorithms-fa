---
tags:
  - Fully Translated
e_maxx_link: sparse-table
---

# Sparse Table

«اسپارس تیبل» (Sparse Table) یه جور ساختمان داده‌ست که بهمون اجازه می‌ده به یه سری سؤالا در مورد یه بازه از اطلاعات (که بهش می‌گن range query) جواب بدیم.
این ساختار داده می‌تونه به بیشتر این سؤالای بازه‌ای توی زمان $O(\log n)$ جواب بده، اما اصل قدرتش جاییه که پای سؤال در مورد کمترین مقدار توی یه بازه (range minimum query) یا بیشترین مقدار (range maximum query) وسط باشه.
برای این مدل سؤالا، می‌تونه جواب رو تو چشم به هم زدن، یعنی توی زمان $O(1)$، حساب کنه.

تنها نقطه ضعفش اینه که فقط روی آرایه‌هایی کار می‌کنه که _عوض نمی‌شن_ (immutable).
یعنی بین دو تا سؤالی که ازش می‌پرسی، نباید به آرایه دست بزنی و چیزی رو توش عوض کنی.
اگه حتی یه دونه از اعضای آرایه تغییر کنه، کل ساختار داده رو باید از اول حساب کتاب کنی.

## برداشت کلی (شهود)

هر عدد مثبتی رو می‌شه به شکل یه مجموع منحصربه‌فرد از توان‌های دو (که از بزرگ به کوچیک مرتب شدن) نشون داد.
این در واقع همون نمایش باینری (دودویی) یه عدده، فقط یه جور دیگه‌ای بهش نگاه می‌کنیم.
مثلاً $13 = (1101)_2 = 8 + 4 + 1$.
برای هر عدد $x$، حداکثر $\lceil \log_2 x \rceil$ تا جمله تو این مجموع وجود داره.

با همین منطق، هر بازه‌ای رو هم می‌شه به صورت یه اجتماع از چند تا بازه که طولشون توان‌های دو هست (و از بزرگ به کوچیک مرتب شدن)، نشون داد.
مثلاً، بازه‌ی $[2, 14]$ رو می‌شه به صورت $[2, 9] \cup [10, 13] \cup [14, 14]$ نوشت، که طول کل بازه ۱۳ و طول بازه‌های جدا از هم به ترتیب ۸، ۴ و ۱ هست.
اینجا هم، این اجتماع حداکثر از $\lceil \log_2(\text{طول بازه}) \rceil$ تا بازه تشکیل شده.

ایده‌ی اصلی پشت اسپارس تیبل اینه که بیایم جواب همه‌ی سؤالا برای بازه‌هایی که طولشون توانی از دو هست رو از قبل حساب کنیم.
این‌جوری وقتی یه سؤال در مورد یه بازه‌ی دلخواه ازمون پرسیدن، اون بازه رو به همون تیکه‌هایی که طولشون توان دو هست می‌شکنیم، جواب‌های از قبل حساب‌شده رو برمی‌داریم و با هم ترکیب می‌کنیم تا به جواب نهایی برسیم.

## پیش‌محاسبه

ما از یه آرایه‌ی دو بعدی استفاده می‌کنیم تا جواب‌های از قبل حساب‌شده رو توش ذخیره کنیم.
خونه‌ی $\text{st}[i][j]$ جواب سؤال برای بازه‌ای به طول $2^i$ که از خونه‌ی $j$ شروع می‌شه (یعنی بازه‌ی $[j, j + 2^i - 1]$) رو نگه می‌داره.
اندازه‌ی این آرایه‌ی دو بعدی $(K + 1) \times \text{MAXN}$ خواهد بود، که $\text{MAXN}$ بزرگ‌ترین طول ممکن برای آرایه است.
مقدار $\text{K}$ باید توی شرط $\text{K} \ge \lfloor \log_2 \text{MAXN} \rfloor$ صدق کنه، چون $2^{\lfloor \log_2 \text{MAXN} \rfloor}$ بزرگ‌ترین بازه‌ایه که طولش توانی از دو هست و ما باید پشتیبانیش کنیم.
برای آرایه‌هایی با طول منطقی (مثلاً تا $10^7$ تا عضو)، $K = 25$ یه گزینه‌ی خوبه.

بعدِ $\text{MAXN}$ رو به عنوان بعد دوم گذاشتیم تا دسترسی به حافظه پشت سر هم و سریع‌تر (سازگار با کش) باشه.

```cpp
int st[K + 1][MAXN];
```

از اونجایی که یه بازه به طول $2^i$ مثل $[j, j + 2^i - 1]$ خیلی خوشگل به دو تا بازه‌ی کوچیک‌تر به طول $2^{i-1}$ (یعنی $[j, j + 2^{i-1} - 1]$ و $[j + 2^{i-1}, j + 2^i - 1]$) تقسیم می‌شه، می‌تونیم با روش برنامه‌نویسی پویا (dynamic programming) این جدول رو خیلی بهینه بسازیم:

```cpp
std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = f(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

تابع $f$ بستگی به این داره که سؤال ما از چه نوعیه.
مثلاً اگه بخوایم جمع یه بازه رو حساب کنیم، این تابع جمع می‌کنه و اگه بخوایم کمترین مقدار رو پیدا کنیم، مینیمم رو حساب می‌کنه.

پیچیدگی زمانی این پیش‌محاسبه هم $O(\text{N} \log \text{N})$ هست.

## سؤال در مورد مجموع یه بازه

تو این مدل سؤالا، می‌خوایم مجموع کل عددهای توی یه بازه رو پیدا کنیم.
برای همین، تعریف طبیعی برای تابع $f$ می‌شه $f(x, y) = x + y$.
می‌تونیم ساختار داده رو این‌جوری بسازیم:

```cpp
long long st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = st[i - 1][j] + st[i - 1][j + (1 << (i - 1))];
```

خب، برای اینکه به سؤال «جمع بازه‌ی $[L, R]$ چقدر می‌شه؟» جواب بدیم، میایم روی همه‌ی توان‌های دو، از بزرگ به کوچیک، حرکت می‌کنیم.
هر وقت یه توانی از دو مثل $2^i$ از طول بازه‌ی ما ($= R - L + 1$) کوچیک‌تر یا مساوی بود، تیکه‌ی اول بازه یعنی $[L, L + 2^i - 1]$ رو پردازش می‌کنیم و با بقیه‌ی بازه یعنی $[L + 2^i, R]$ ادامه می‌دیم.

```cpp
long long sum = 0;
for (int i = K; i >= 0; i--) {
    if ((1 << i) <= R - L + 1) {
        sum += st[i][L];
        L += 1 << i;
    }
}
```

پیچیدگی زمانی برای یه سؤال مجموع بازه $O(K) = O(\log \text{MAXN})$ هست.

## سؤال در مورد کمترین مقدار در بازه (RMQ)

این همون جاییه که اسپارس تیبل قدرت واقعی‌شو نشون می‌ده.
وقتی می‌خوایم کمترین مقدار توی یه بازه رو حساب کنیم، خیلی مهم نیست که یه عدد رو یه بار حساب کنیم یا دو بار (چون کمینه فرقی نمی‌کنه).
واسه همین، به جای اینکه بازه رو به چند تا تیکه‌ی جدا از هم بشکنیم، می‌تونیم خیلی راحت اونو فقط به دو تا بازه‌ی *هم‌پوشان* با طول توانی از دو تقسیم کنیم.
مثلاً، بازه‌ی $[1,6]$ رو می‌تونیم به بازه‌های $[1,4]$ و $[3,6]$ تقسیم کنیم.
کمترین مقدار بازه‌ی $[1,6]$، دقیقاً با کمینه‌ی «کمترین مقدار بازه‌ی $[1,4]$» و «کمترین مقدار بازه‌ی $[3,6]$» برابره.
پس می‌تونیم کمترین مقدار بازه‌ی $[L, R]$ رو با این فرمول حساب کنیم:

$$\min(\text{st}[i][L], \text{st}[i][R - 2^i + 1]) \quad \text{ where } i = \log_2(R - L + 1)$$

این روش به این احتیاج داره که بتونیم $\log_2(R - L + 1)$ رو سریع حساب کنیم.
می‌تونید این کار رو با پیش‌محاسبه کردن همه‌ی لگاریتم‌ها انجام بدید:

```cpp
int lg[MAXN+1];
lg[1] = 0;
for (int i = 2; i <= MAXN; i++)
    lg[i] = lg[i/2] + 1;
```
یه راه دیگه اینه که لگاریتم رو همون لحظه (on the fly) با فضا و زمان ثابت حساب کنی:
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
[این بنچمارک](https://quick-bench.com/q/Zghbdj_TEkmw4XG2nqOpD3tsJ8U) نشون می‌ده که استفاده از آرایه‌ی `lg` به خاطر خطاهای حافظه کش (cache misses) کندتره.

بعدش باید ساختار اسپارس تیبل رو پیش‌محاسبه کنیم. این بار $f$ رو با $f(x, y) = \min(x, y)$ تعریف می‌کنیم.

```cpp
int st[K + 1][MAXN];

std::copy(array.begin(), array.end(), st[0]);

for (int i = 1; i <= K; i++)
    for (int j = 0; j + (1 << i) <= N; j++)
        st[i][j] = min(st[i - 1][j], st[i - 1][j + (1 << (i - 1))]);
```

و کمترین مقدار بازه‌ی $[L, R]$ رو می‌شه این‌جوری حساب کرد:

```cpp
int i = lg[R - L + 1];
int minimum = min(st[i][L], st[i][R - (1 << i) + 1]);
```

پیچیدگی زمانی برای یه سؤال کمینه بازه‌ای، $O(1)$ هست.

## ساختار داده‌های مشابه با پشتیبانی از سؤالای بیشتر

یکی از ضعف‌های اصلی اون روش $O(1)$ که بالا گفتیم اینه که فقط برای سؤال‌هایی کار می‌کنه که تابعشون [خودتوان (idempotent)](https://en.wikipedia.org/wiki/Idempotence) باشه.
یعنی برای پیدا کردن کمترین و بیشترین مقدار توی بازه عالیه، اما نمی‌شه باهاش جمع اعضای یه بازه رو حساب کرد.

ساختار داده‌های مشابه‌ای هستن که می‌تونن هر نوع تابع شرکت‌پذیری رو مدیریت کنن و به سؤالای بازه‌ای تو زمان $O(1)$ جواب بدن.
یکی از اونا اسمش [Disjoint Sparse Table](https://discuss.codechef.com/questions/117696/tutorial-disjoint-sparse-table) هست.
یه مورد دیگه هم [Sqrt Tree](sqrt-tree.md) هست.

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