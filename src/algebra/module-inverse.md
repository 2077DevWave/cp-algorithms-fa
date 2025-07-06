---
tags:
  - AI Translated
e_maxx_link: module-inverse
---

# معکوس ضربی پیمانه‌ای

## تعریف

یک [معکوس ضربی پیمانه‌ای](http://en.wikipedia.org/wiki/Modular_multiplicative_inverse) برای یک عدد صحیح $a$، یک عدد صحیح $x$ است به طوری که $a \cdot x$ به پیمانه $m$ با $1$ هم‌نهشت باشد.
به صورت رسمی، ما می‌خواهیم یک عدد صحیح $x$ پیدا کنیم که:

$$a \cdot x \equiv 1 \mod m.$$

ما همچنین $x$ را به سادگی با $a^{-1}$ نمایش می‌دهیم.

باید توجه داشت که معکوس پیمانه‌ای همیشه وجود ندارد. برای مثال، فرض کنید $m = 4$ و $a = 2$. 
با بررسی تمام مقادیر ممکن به پیمانه $m$، مشخص می‌شود که نمی‌توانیم $a^{-1}$ ای پیدا کنیم که در معادله بالا صدق کند. 
می‌توان اثبات کرد که معکوس پیمانه‌ای وجود دارد اگر و تنها اگر $a$ و $m$ نسبت به هم اول باشند (یعنی $\gcd(a, m) = 1$).

در این مقاله، ما دو روش برای یافتن معکوس پیمانه‌ای در صورت وجود آن، و یک روش برای یافتن معکوس پیمانه‌ای برای همه اعداد در زمان خطی ارائه می‌دهیم.

## یافتن معکوس ضربی پیمانه‌ای با استفاده از الگوریتم اقلیدسی تعمیم‌یافته

معادله زیر را در نظر بگیرید (با مجهول‌های $x$ و $y$):

$$a \cdot x + m \cdot y = 1$$

این یک [معادله سیاله خطی با دو متغیر](linear-diophantine-equation.md) است.
همانطور که در مقاله لینک شده نشان داده شده است، زمانی که $\gcd(a, m) = 1$ باشد، معادله دارای یک جواب است که می‌توان آن را با استفاده از [الگوریتم اقلیدسی تعمیم‌یافته](extended-euclid-algorithm.md) پیدا کرد.
توجه داشته باشید که $\gcd(a, m) = 1$ شرط وجود معکوس پیمانه‌ای نیز هست.

حال، اگر از دو طرف معادله به پیمانه $m$ باقی‌مانده بگیریم، می‌توانیم از $m \cdot y$ خلاص شویم و معادله به شکل زیر در می‌آید:

$$a \cdot x \equiv 1 \mod m$$

بنابراین، معکوس پیمانه‌ای $a$ همان $x$ است.

پیاده‌سازی به صورت زیر است:

```cpp
int x, y;
int g = extended_euclidean(a, m, x, y);
if (g != 1) {
    cout << "No solution!";
}
else {
    x = (x % m + m) % m;
    cout << x << endl;
}
```

به نحوه تغییر `x` توجه کنید.
`x` حاصل از الگوریتم اقلیدسی تعمیم‌یافته ممکن است منفی باشد، بنابراین `x % m` نیز ممکن است منفی شود و ما ابتدا باید `m` را به آن اضافه کنیم تا مثبت شود.

<div id="fermat-euler"></div>
## یافتن معکوس ضربی پیمانه‌ای با استفاده از توان‌رسانی دودویی

روش دیگر برای یافتن معکوس پیمانه‌ای استفاده از قضیه اویلر است که بیان می‌کند هم‌نهشتی زیر برقرار است اگر $a$ و $m$ نسبت به هم اول باشند:

$$a^{\phi (m)} \equiv 1 \mod m$$

$\phi$ [تابع فی اویلر](phi-function.md) است.
باز هم توجه داشته باشید که نسبت به هم اول بودن $a$ و $m$ شرط وجود معکوس پیمانه‌ای نیز بود.

اگر $m$ یک عدد اول باشد، این قضیه به [قضیه کوچک فرما](http://en.wikipedia.org/wiki/Fermat's_little_theorem) ساده می‌شود:

$$a^{m - 1} \equiv 1 \mod m$$

دو طرف معادلات بالا را در $a^{-1}$ ضرب می‌کنیم و به نتایج زیر می‌رسیم:

*   برای یک پیمانه دلخواه (اما متباین با $a$) $m$: $a ^ {\phi (m) - 1} \equiv a ^{-1} \mod m$
*   برای یک پیمانه اول $m$: $a ^ {m - 2} \equiv a ^ {-1} \mod m$

از این نتایج، می‌توانیم به راحتی معکوس پیمانه‌ای را با استفاده از [الگوریتم توان‌رسانی دودویی](binary-exp.md) که در زمان $O(\log m)$ کار می‌کند، پیدا کنیم.

اگرچه این روش نسبت به روشی که در پاراگراف قبل توضیح داده شد، فهم ساده‌تری دارد، اما در حالتی که $m$ یک عدد اول نباشد، ما نیاز به محاسبه تابع فی اویلر داریم که شامل تجزیه $m$ به عوامل اول است و این کار ممکن است بسیار دشوار باشد. اگر تجزیه اول $m$ مشخص باشد، پیچیدگی این روش $O(\log m)$ است.

<div id="finding-the-modular-inverse-using-euclidean-division"></div>
## یافتن معکوس ضربی پیمانه‌ای برای پیمانه‌های اول با استفاده از تقسیم اقلیدسی

با فرض پیمانه اول $m > a$ (یا می‌توانیم با یک بار اعمال پیمانه، آن را کوچکتر کنیم)، بر اساس [تقسیم اقلیدسی](https://en.wikipedia.org/wiki/Euclidean_division) داریم:

$$m = k \cdot a + r$$

که در آن $k = \left\lfloor \frac{m}{a} \right\rfloor$ و $r = m \bmod a$ است. سپس:

$$
\begin{align*}
& \implies & 0          & \equiv k \cdot a + r   & \mod m \\
& \iff & r              & \equiv -k \cdot a      & \mod m \\
& \iff & r \cdot a^{-1} & \equiv -k              & \mod m \\
& \iff & a^{-1}         & \equiv -k \cdot r^{-1} & \mod m
\end{align*}
$$

توجه داشته باشید که این استدلال در صورتی که $m$ اول نباشد، معتبر نیست، زیرا وجود $a^{-1}$ لزوماً وجود $r^{-1}$ را در حالت کلی تضمین نمی‌کند. برای درک این موضوع، بیایید سعی کنیم $5^{-1}$ به پیمانه $12$ را با فرمول بالا محاسبه کنیم. ما انتظار داریم به عدد $5$ برسیم، زیرا $5 \cdot 5 \equiv 1 \bmod 12$. اما، $12 = 2 \cdot 5 + 2$ است و ما $k=2$ و $r=2$ داریم که $2$ به پیمانه $12$ معکوس‌پذیر نیست.

اما اگر پیمانه اول باشد، تمام اعداد $a$ با شرط $0 < a < m$ به پیمانه $m$ معکوس‌پذیر هستند و می‌توانیم تابع بازگشتی زیر (در C++) را برای محاسبه معکوس پیمانه‌ای عدد $a$ نسبت به $m$ داشته باشیم:

```cpp {.cpp file=modular_inverse_euclidean_division}
int inv(int a) {
  return a <= 1 ? a : m - (long long)(m/a) * inv(m % a) % m;
}
```

پیچیدگی زمانی دقیق این بازگشت مشخص نیست. این مقدار جایی بین $O(\frac{\log m}{\log\log m})$ و $O(m^{\frac{1}{3} - \frac{2}{177} + \epsilon})$ است. به [On the length of Pierce expansions](https://arxiv.org/abs/2211.08374) مراجعه کنید. در عمل این پیاده‌سازی سریع است، به عنوان مثال برای پیمانه $10^9 + 7$ همیشه در کمتر از 50 تکرار به پایان می‌رسد.

<div id="mod-inv-all-num"></div>
با استفاده از این فرمول، می‌توانیم معکوس پیمانه‌ای را برای هر عدد در بازه $[1, m-1]$ در زمان $O(m)$ پیش‌محاسبه کنیم.

```cpp {.cpp file=modular_inverse_euclidean_division_all}
inv[1] = 1;
for(int a = 2; a < m; ++a)
    inv[a] = m - (long long)(m/a) * inv[m%a] % m;
```

## یافتن معکوس ضربی پیمانه‌ای برای آرایه‌ای از اعداد به پیمانه m

فرض کنید یک آرایه به ما داده شده و می‌خواهیم معکوس پیمانه‌ای را برای تمام اعداد آن (که همگی معکوس‌پذیر هستند) پیدا کنیم.
به جای محاسبه معکوس برای هر عدد به صورت جداگانه، می‌توانیم کسر را با حاصل‌ضرب پیشوندی (بدون خود عدد) و حاصل‌ضرب پسوندی (بدون خود عدد) بسط دهیم و در نهایت فقط یک معکوس را محاسبه کنیم.

$$
\begin{align}
x_i^{-1} &= \frac{1}{x_i} = \frac{\overbrace{x_1 \cdot x_2 \cdots x_{i-1}}^{\text{پیشوندی}_{i-1}} \cdot ~1~ \cdot \overbrace{x_{i+1} \cdot x_{i+2} \cdots x_n}^{\text{پسوندی}_{i+1}}}{x_1 \cdot x_2 \cdots x_{i-1} \cdot x_i \cdot x_{i+1} \cdot x_{i+2} \cdots x_n} \\
&= \text{پیشوندی}_{i-1} \cdot \text{پسوندی}_{i+1} \cdot \left(x_1 \cdot x_2 \cdots x_n\right)^{-1}
\end{align}
$$

در کد، می‌توانیم یک آرایه حاصل‌ضرب پیشوندی بسازیم (با حذف خود عنصر و شروع از عنصر همانی)، معکوس پیمانه‌ای حاصل‌ضرب تمام اعداد را محاسبه کرده و سپس آن را در حاصل‌ضرب پیشوندی و حاصل‌ضرب پسوندی (با حذف خود عنصر) ضرب کنیم. حاصل‌ضرب پسوندی با پیمایش از انتها به ابتدا محاسبه می‌شود.

```cpp
std::vector<int> invs(const std::vector<int> &a, int m) {
    int n = a.size();
    if (n == 0) return {};
    std::vector<int> b(n);
    int v = 1;
    for (int i = 0; i != n; ++i) {
        b[i] = v;
        v = static_cast<long long>(v) * a[i] % m;
    }
    int x, y;
    extended_euclidean(v, m, x, y);
    x = (x % m + m) % m;
    for (int i = n - 1; i >= 0; --i) {
        b[i] = static_cast<long long>(x) * b[i] % m;
        x = static_cast<long long>(x) * a[i] % m;
    }
    return b;
}
```

## مسائل تمرینی

* [UVa 11904 - One Unit Machine](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3055)
* [Hackerrank - Longest Increasing Subsequence Arrays](https://www.hackerrank.com/contests/world-codesprint-5/challenges/longest-increasing-subsequence-arrays)
* [Codeforces 300C - Beautiful Numbers](http://codeforces.com/problemset/problem/300/C)
* [Codeforces 622F - The Sum of the k-th Powers](http://codeforces.com/problemset/problem/622/F)
* [Codeforces 717A - Festival Organization](http://codeforces.com/problemset/problem/717/A)
* [Codeforces 896D - Nephren Runs a Cinema](http://codeforces.com/problemset/problem/896/D)