---
tags:
  - AI Translated
e_maxx_link: phi-function
---

# تابع فی اویلر

تابع فی اویلر، که با نام **تابع فی** $\phi (n)$ نیز شناخته می‌شود، تعداد اعداد صحیح در بازه ۱ تا $n$ (شامل خود ۱ و $n$) را می‌شمارد که نسبت به $n$ اول هستند. دو عدد را نسبت به هم اول (متباین) می‌گوییم هرگاه بزرگ‌ترین مقسوم‌علیه مشترک آن‌ها برابر با ۱ باشد ($۱$ نسبت به هر عددی اول در نظر گرفته می‌شود).

در ادامه مقادیر $\phi(n)$ برای چند عدد صحیح مثبت اول آمده است:

$$\begin{array}{|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|c|}
\hline
n & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 & 17 & 18 & 19 & 20 & 21 \\\\ \hline
\phi(n) & 1 & 1 & 2 & 2 & 4 & 2 & 6 & 4 & 6 & 4 & 10 & 4 & 12 & 6 & 8 & 8 & 16 & 6 & 18 & 8 & 12 \\\\ \hline
\end{array}$$

## ویژگی‌ها

ویژگی‌های زیر برای محاسبه تابع فی اویلر برای هر عددی کافی هستند:

  - اگر $p$ یک عدد اول باشد، آنگاه برای تمام $q$ های در بازه $1 \le q < p$ داریم $\gcd(p, q) = 1$. بنابراین:
  
$$\phi (p) = p - 1.$$

  - اگر $p$ یک عدد اول و $k \ge 1$ باشد، آنگاه دقیقاً $p^k / p$ عدد بین ۱ و $p^k$ وجود دارد که بر $p$ بخش‌پذیر هستند.
    این به ما نتیجه زیر را می‌دهد:
    
$$\phi(p^k) = p^k - p^{k-1}.$$

  - اگر $a$ و $b$ نسبت به هم اول باشند، آنگاه:
    
    \[\phi(a b) = \phi(a) \cdot \phi(b).\]
    
    اثبات این رابطه بدیهی نیست و از [قضیه باقیمانده چینی](chinese-remainder-theorem.md) نتیجه می‌شود. قضیه باقیمانده چینی تضمین می‌کند که برای هر $0 \le x < a$ و هر $0 \le y < b$، یک $z$ یکتای $0 \le z < a b$ وجود دارد که $z \equiv x \pmod{a}$ و $z \equiv y \pmod{b}$ است. به راحتی می‌توان نشان داد که $z$ نسبت به $ab$ اول است اگر و تنها اگر $x$ نسبت به $a$ و $y$ نسبت به $b$ اول باشند. بنابراین، تعداد اعداد صحیح اول نسبت به $ab$ برابر با حاصل‌ضرب تعداد اعداد اول نسبت به $a$ و $b$ است.

  - به طور کلی، برای $a$ و $b$ که نسبت به هم اول نیستند، معادله زیر برقرار است:

    \[\phi(ab) = \phi(a) \cdot \phi(b) \cdot \dfrac{d}{\phi(d)}\]

    که در آن $d = \gcd(a, b)$ است.

بنابراین، با استفاده از سه ویژگی اول، می‌توانیم $\phi(n)$ را از طریق تجزیه $n$ به عوامل اول (تجزیه $n$ به حاصل‌ضرب عوامل اولش) محاسبه کنیم.
اگر $n = {p_1}^{a_1} \cdot {p_2}^{a_2} \cdots {p_k}^{a_k}$ که در آن $p_i$ ها عوامل اول $n$ هستند،

$$\begin{align}
\phi (n) &= \phi ({p_1}^{a_1}) \cdot \phi ({p_2}^{a_2}) \cdots  \phi ({p_k}^{a_k}) \\\\
&= \left({p_1}^{a_1} - {p_1}^{a_1 - 1}\right) \cdot \left({p_2}^{a_2} - {p_2}^{a_2 - 1}\right) \cdots \left({p_k}^{a_k} - {p_k}^{a_k - 1}\right) \\\\
&= p_1^{a_1} \cdot \left(1 - \frac{1}{p_1}\right) \cdot p_2^{a_2} \cdot \left(1 - \frac{1}{p_2}\right) \cdots p_k^{a_k} \cdot \left(1 - \frac{1}{p_k}\right) \\\\
&= n \cdot \left(1 - \frac{1}{p_1}\right) \cdot \left(1 - \frac{1}{p_2}\right) \cdots \left(1 - \frac{1}{p_k}\right)
\end{align}$$

## پیاده‌سازی

در اینجا پیاده‌سازی با استفاده از تجزیه به عوامل اول با پیچیدگی $O(\sqrt{n})$ آمده است:

```cpp
int phi(int n) {
    int result = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0)
                n /= i;
            result -= result / i;
        }
    }
    if (n > 1)
        result -= result / n;
    return result;
}
```

## محاسبه تابع فی اویلر از ۱ تا $n$ در $O(n \log\log{n})$ { #etf_1_to_n data-toc-label="محاسبه تابع فی اویلر از ۱ تا n در <script type=\"math/tex\">O(n log log n)</script>" }

اگر به مقدار فی برای تمام اعداد بین ۱ تا $n$ نیاز داشته باشیم، تجزیه تمام $n$ عدد کارآمد نیست.
می‌توانیم از ایده‌ای مشابه [غربال اراتوستن](sieve-of-eratosthenes.md) استفاده کنیم.
این روش هنوز بر اساس ویژگی نشان داده شده در بالا است، اما به جای به‌روزرسانی نتیجه موقت برای هر عامل اول برای هر عدد، تمام اعداد اول را پیدا می‌کنیم و برای هر کدام، نتایج موقت تمام اعدادی را که بر آن عدد اول بخش‌پذیر هستند، به‌روزرسانی می‌کنیم.

از آنجایی که این رویکرد اساساً با غربال اراتوستن یکسان است، پیچیدگی زمانی آن نیز یکسان خواهد بود: $O(n \log \log n)$

```cpp
void phi_1_to_n(int n) {
    vector<int> phi(n + 1);
    for (int i = 0; i <= n; i++)
        phi[i] = i;
    
    for (int i = 2; i <= n; i++) {
        if (phi[i] == i) {
            for (int j = i; j <= n; j += i)
                phi[j] -= phi[j] / i;
        }
    }
}
```


## ویژگی مجموع مقسوم‌علیه‌ها { #divsum}

این ویژگی جالب توسط گاوس اثبات شده است:

$$ \sum_{d|n} \phi{(d)} = n$$

در اینجا مجموع روی تمام مقسوم‌علیه‌های مثبت $d$ از $n$ است.

به عنوان مثال، مقسوم‌علیه‌های 10 عبارتند از ۱، ۲، ۵ و ۱۰.
از این رو $\phi{(1)} + \phi{(2)} + \phi{(5)} + \phi{(10)} = 1 + 1 + 4 + 4 = 10$.

### یافتن فی از ۱ تا $n$ با استفاده از ویژگی مجموع مقسوم‌علیه‌ها { data-toc-label="یافتن فی از ۱ تا n با استفاده از ویژگی مجموع مقسوم‌علیه‌ها" }

ویژگی مجموع مقسوم‌علیه‌ها همچنین به ما امکان محاسبه فی تمام اعداد بین ۱ تا $n$ را می‌دهد.
این پیاده‌سازی کمی ساده‌تر از پیاده‌سازی قبلی مبتنی بر غربال اراتوستن است، اما پیچیدگی زمانی آن کمی بدتر است: $O(n \log n)$

```cpp
void phi_1_to_n(int n) {
    vector<int> phi(n + 1);
    phi[0] = 0;
    phi[1] = 1;
    for (int i = 2; i <= n; i++)
        phi[i] = i - 1;
    
    for (int i = 2; i <= n; i++)
        for (int j = 2 * i; j <= n; j += i)
              phi[j] -= phi[i];
}
```

## کاربرد در قضیه اویلر { #application }

مشهورترین و مهم‌ترین ویژگی تابع فی اویلر در **قضیه اویلر** بیان شده است: 

$$a^{\phi(m)} \equiv 1 \pmod m \quad \text{اگر } a \text{ و } m \text{ نسبت به هم اول باشند.}$$

در حالت خاصی که $m$ اول باشد، قضیه اویلر به **قضیه کوچک فرما** تبدیل می‌شود:

$$a^{m - 1} \equiv 1 \pmod m$$

قضیه اویلر و تابع فی اویلر اغلب در کاربردهای عملی ظاهر می‌شوند، به عنوان مثال هر دو برای محاسبه [وارون ضربی به پیمانه](module-inverse.md) استفاده می‌شوند.

به عنوان یک نتیجه فوری، به هم‌ارزی زیر نیز می‌رسیم:

$$a^n \equiv a^{n \bmod \phi(m)} \pmod m$$

این رابطه امکان محاسبه $x^n \bmod m$ را برای $n$ های بسیار بزرگ فراهم می‌کند، به خصوص اگر $n$ نتیجه یک محاسبه دیگر باشد، زیرا به ما امکان می‌دهد $n$ را تحت یک پیمانه محاسبه کنیم.

### نظریه گروه‌ها
$\phi(n)$ برابر است با [مرتبه گروه ضربی به پیمانه n](https://en.wikipedia.org/wiki/Multiplicative_group_of_integers_modulo_n) $(\mathbb Z / n\mathbb Z)^\times$، یعنی گروه یکه‌ها (عناصری که وارون ضربی دارند). عناصری که وارون ضربی دارند دقیقاً همان‌هایی هستند که نسبت به $n$ اول هستند.

[مرتبه ضربی](https://en.wikipedia.org/wiki/Multiplicative_order) یک عنصر $a$ به پیمانه $n$ که با $\operatorname{ord}_n(a)$ نمایش داده می‌شود، کوچک‌ترین $k>0$ است به طوری که $a^k \equiv 1 \pmod m$. $\operatorname{ord}_n(a)$ اندازه زیرگروه تولید شده توسط $a$ است، بنابراین طبق قضیه لاگرانژ، مرتبه ضربی هر عنصر $a$ باید $\phi(n)$ را بشمارد. اگر مرتبه ضربی $a$ برابر با $\phi(n)$ باشد، که بزرگ‌ترین مقدار ممکن است، آنگاه $a$ یک [ریشه اولیه](primitive-root.md) است و گروه طبق تعریف، دوری است.

## تعمیم

نسخه کمتر شناخته شده‌ای از هم‌ارزی آخر وجود دارد که امکان محاسبه کارآمد $x^n \bmod m$ را برای $x$ و $m$ که نسبت به هم اول نیستند، فراهم می‌کند.
برای $x$ و $m$ دلخواه و $n \geq \log_2 m$:

$$x^{n}\equiv x^{\phi(m)+[n \bmod \phi(m)]} \mod m$$

اثبات:

فرض کنید $p_1, \dots, p_t$ مقسوم‌علیه‌های اول مشترک $x$ و $m$ باشند و $k_i$ توان آن‌ها در $m$ باشد.
با این‌ها $a = p_1^{k_1} \dots p_t^{k_t}$ را تعریف می‌کنیم، که باعث می‌شود $\frac{m}{a}$ نسبت به $x$ اول باشد.
و فرض کنید $k$ کوچک‌ترین عددی باشد که $a$ عدد $x^k$ را بشمارد.
با فرض $n \ge k$ می‌توانیم بنویسیم:

$$\begin{align}x^n \bmod m &= \frac{x^k}{a}ax^{n-k}\bmod m \\
&= \frac{x^k}{a}\left(ax^{n-k}\bmod m\right) \bmod m \\
&= \frac{x^k}{a}\left(ax^{n-k}\bmod a \frac{m}{a}\right) \bmod m \\
&=\frac{x^k}{a} a \left(x^{n-k} \bmod \frac{m}{a}\right)\bmod m \\
&= x^k\left(x^{n-k} \bmod \frac{m}{a}\right)\bmod m
\end{align}$$

هم‌ارزی بین خط سوم و چهارم از این واقعیت ناشی می‌شود که $ab \bmod ac = a(b \bmod c)$. در واقع اگر $b = cd + r$ با $r < c$ باشد، آنگاه $ab = acd + ar$ با $ar < ac$.

از آنجایی که $x$ و $\frac{m}{a}$ نسبت به هم اول هستند، می‌توانیم قضیه اویلر را به کار ببریم و فرمول کارآمد زیر را به دست آوریم (کارآمد است چون $k$ بسیار کوچک است؛ در واقع $k \le \log_2 m$):

$$x^n \bmod m = x^k\left(x^{n-k \bmod \phi(\frac{m}{a})} \bmod \frac{m}{a}\right)\bmod m.$$

به کار بردن این فرمول دشوار است، اما می‌توانیم از آن برای تحلیل رفتار $x^n \bmod m$ استفاده کنیم. می‌توان دید که دنباله توان‌ها $(x^1 \bmod m, x^2 \bmod m, x^3 \bmod m, \dots)$ پس از $k$ عنصر اول (یا کمتر) وارد یک دور به طول $\phi\left(\frac{m}{a}\right)$ می‌شود.
$\phi\left(\frac{m}{a}\right)$ عدد $\phi(m)$ را می‌شمارد (زیرا $a$ و $\frac{m}{a}$ نسبت به هم اول هستند و داریم $\phi(a) \cdot \phi\left(\frac{m}{a}\right) = \phi(m)$)، بنابراین می‌توانیم بگوییم که دوره تناوب طولی برابر با $\phi(m)$ دارد.
و از آنجایی که $\phi(m) \ge \log_2 m \ge k$ است، می‌توانیم فرمول مطلوب و بسیار ساده‌تر زیر را نتیجه بگیریم:

$$ x^n \equiv x^{\phi(m)} x^{(n - \phi(m)) \bmod \phi(m)} \bmod m \equiv x^{\phi(m)+[n \bmod \phi(m)]} \mod m.$$

## مسائل تمرینی

* [SPOJ #4141 "Euler Totient Function" [سختی: بسیار آسان]](http://www.spoj.com/problems/ETF/)
* [UVA #10179 "Irreducible Basic Fractions" [سختی: آسان]](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1120)
* [UVA #10299 "Relatives" [سختی: آسان]](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1240)
* [UVA #11327 "Enumerating Rational Numbers" [سختی: متوسط]](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2302)
* [TIMUS #1673 "Admission to Exam" [سختی: زیاد]](http://acm.timus.ru/problem.aspx?space=1&num=1673)
* [UVA 10990 - Another New Function](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1931)
* [Codechef - Golu and Sweetness](https://www.codechef.com/problems/COZIE)
* [SPOJ - LCM Sum](http://www.spoj.com/problems/LCMSUM/)
* [GYM - Simple Calculations (F)](http://codeforces.com/gym/100975)
* [UVA 13132 - Laser Mirrors](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=5043)
* [SPOJ - GCDEX](http://www.spoj.com/problems/GCDEX/)
* [UVA 12995 - Farey Sequence](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=4878)
* [SPOJ - Totient in Permutation (easy)](http://www.spoj.com/problems/TIP1/)
* [LOJ - Mathematically Hard](http://lightoj.com/volume_showproblem.php?problem=1007)
* [SPOJ - Totient Extreme](http://www.spoj.com/problems/DCEPCA03/)
* [SPOJ - Playing with GCD](http://www.spoj.com/problems/NAJPWG/)
* [SPOJ - G Force](http://www.spoj.com/problems/DCEPC12G/)
* [SPOJ - Smallest Inverse Euler Totient Function](http://www.spoj.com/problems/INVPHI/)
* [Codeforces - Power Tower](http://codeforces.com/problemset/problem/906/D)
* [Kattis - Exponial](https://open.kattis.com/problems/exponial)
* [LeetCode - 372. Super Pow](https://leetcode.com/problems/super-pow/)
* [Codeforces - The Holmes Children](http://codeforces.com/problemset/problem/776/E)
* [Codeforces - Small GCD](https://codeforces.com/contest/1900/problem/D)