---
tags:
  - AI Translated
e_maxx_link: garners-algorithm
---

# الگوریتم گارنر

یکی از نتایج [قضیه باقیمانده چینی](chinese-remainder-theorem.md) این است که می‌توانیم اعداد بزرگ را با استفاده از آرایه‌ای از اعداد صحیح کوچک نمایش دهیم.
برای مثال، فرض کنید $p$ حاصل‌ضرب ۱۰۰۰ عدد اول نخست باشد. عدد $p$ حدود ۳۰۰۰ رقم دارد.

هر عدد $a$ کوچک‌تر از $p$ را می‌توان به صورت آرایه‌ای از $a_1, \ldots, a_k$ نمایش داد، به طوری که $a_i \equiv a \pmod{p_i}$.
اما برای انجام این کار، بدیهی است که باید بدانیم چگونه عدد $a$ را از این نمایش بازگردانیم.
یک راه در مقاله مربوط به قضیه باقیمانده چینی مورد بحث قرار گرفته است.

در این مقاله، ما یک جایگزین، یعنی الگوریتم گارنر را بررسی می‌کنیم که برای این منظور نیز قابل استفاده است.

## نمایش مبنای مختلط

می‌توانیم عدد $a$ را در نمایش **مبنای مختلط** (mixed radix) نشان دهیم:

$$a = x_1 + x_2 p_1 + x_3 p_1 p_2 + \ldots + x_k p_1 \cdots p_{k-1} \text{ با شرط } x_i \in [0, p_i)$$

نمایش مبنای مختلط یک دستگاه اعداد جایگاهی است که تعمیمی از دستگاه‌های اعداد معمولی مانند دستگاه اعداد دودویی یا دستگاه اعداد ده‌دهی است.
به عنوان مثال، دستگاه اعداد ده‌دهی یک دستگاه اعداد جایگاهی با مبنای (radix یا base) ۱۰ است.
هر عدد به صورت رشته‌ای از ارقام $d_1 d_2 d_3 \dots d_n$ بین ۰ تا ۹ نمایش داده می‌شود.
برای مثال، رشته‌ی $415$ نشان‌دهنده عدد $4 \cdot 10^2 + 1 \cdot 10^1 + 5 \cdot 10^0$ است.
به طور کلی، رشته ارقام $d_1 d_2 d_3 \dots d_n$ در دستگاه اعداد جایگاهی با مبنای $b$، عدد $d_1 b^{n-1} + d_2 b^{n-2} + \cdots + d_n b^0$ را نمایش می‌دهد.

در یک دستگاه مبنای مختلط، دیگر یک مبنای واحد نداریم. مبنا از یک جایگاه به جایگاه دیگر متغیر است.

## الگوریتم گارنر

الگوریتم گارنر ارقام $x_1, \ldots, x_k$ را محاسبه می‌کند.
توجه داشته باشید که این ارقام نسبتاً کوچک هستند.
رقم $x_i$ یک عدد صحیح بین $0$ و $p_i - 1$ است.

فرض کنید $r_{ij}$ وارون $p_i$ به پیمانه $p_j$ باشد:

$$r_{ij} = (p_i)^{-1} \pmod{p_j}$$

که با استفاده از الگوریتم توصیف شده در [وارون پیمانه‌ای](module-inverse.md) قابل محاسبه است.

با جایگزین کردن $a$ از نمایش مبنای مختلط در اولین معادله همنهشتی، به دست می‌آوریم:

$$a_1 \equiv x_1 \pmod{p_1}.$$

با جایگزینی در معادله دوم به دست می‌آید:

$$a_2 \equiv x_1 + x_2 p_1 \pmod{p_2},$$

که می‌توان آن را با کم کردن $x_1$ و تقسیم بر $p_1$ به صورت زیر بازنویسی کرد:

$$\begin{array}{rclr}
    a_2 - x_1 &\equiv& x_2 p_1 &\pmod{p_2} \\
    (a_2 - x_1) r_{12} &\equiv& x_2 &\pmod{p_2} \\
    x_2 &\equiv& (a_2 - x_1) r_{12} &\pmod{p_2}
\end{array}$$

به طور مشابه، به دست می‌آوریم که:

$$x_3 \equiv ((a_3 - x_1) r_{13} - x_2) r_{23} \pmod{p_3}.$$

اکنون می‌توانیم الگوی در حال ظهور را به وضوح ببینیم که با کد زیر قابل بیان است:

```cpp
for (int i = 0; i < k; ++i) {
    x[i] = a[i];
    for (int j = 0; j < i; ++j) {
        x[i] = r[j][i] * (x[i] - x[j]);

        x[i] = x[i] % p[i];
        if (x[i] < 0)
            x[i] += p[i];
    }
}
```

بنابراین یاد گرفتیم چگونه ارقام $x_i$ را در زمان $O(k^2)$ محاسبه کنیم. عدد $a$ اکنون می‌تواند با استفاده از فرمول ذکر شده قبلی محاسبه شود:

$$a = x_1 + x_2 \cdot p_1 + x_3 \cdot p_1 \cdot p_2 + \ldots + x_k \cdot p_1 \cdots p_{k-1}$$

شایان ذکر است که در عمل، تقریباً همیشه نیاز داریم که پاسخ $a$ را با استفاده از [محاسبات با دقت دلخواه](big-integer.md) محاسبه کنیم، اما ارقام $x_i$ (چون کوچک هستند) معمولاً می‌توانند با استفاده از انواع داده داخلی محاسبه شوند و بنابراین الگوریتم گارنر بسیار کارآمد است.

## پیاده‌سازی الگوریتم گارنر

پیاده‌سازی این الگوریتم با استفاده از Java راحت است، زیرا این زبان از طریق کلاس `BigInteger` از اعداد بزرگ پشتیبانی داخلی دارد.

در اینجا پیاده‌سازی را نشان می‌دهیم که می‌تواند اعداد بزرگ را به صورت مجموعه‌ای از معادلات همنهشتی ذخیره کند.
این پیاده‌سازی از عملیات جمع، تفریق و ضرب پشتیبانی می‌کند.
و با الگوریتم گارنر می‌توانیم مجموعه‌ی معادلات را به یک عدد صحیح منحصر به فرد تبدیل کنیم.
در این کد، ما ۱۰۰ عدد اول بزرگ‌تر از $10^9$ را در نظر می‌گیریم که امکان نمایش اعدادی به بزرگی $10^{900}$ را فراهم می‌کند.

```java
final int SZ = 100;
int pr[] = new int[SZ];
int r[][] = new int[SZ][SZ];

void init() {
    for (int x = 1000 * 1000 * 1000, i = 0; i < SZ; ++x)
        if (BigInteger.valueOf(x).isProbablePrime(100))
            pr[i++] = x;

    for (int i = 0; i < SZ; ++i)
        for (int j = i + 1; j < SZ; ++j)
            r[i][j] =
                BigInteger.valueOf(pr[i]).modInverse(BigInteger.valueOf(pr[j])).intValue();
}

class Number {
    int a[] = new int[SZ];

    public Number() {
    }

    public Number(int n) {
        for (int i = 0; i < SZ; ++i)
            a[i] = n % pr[i];
    }

    public Number(BigInteger n) {
        for (int i = 0; i < SZ; ++i)
            a[i] = n.mod(BigInteger.valueOf(pr[i])).intValue();
    }

    public Number add(Number n) {
        Number result = new Number();
        for (int i = 0; i < SZ; ++i)
            result.a[i] = (a[i] + n.a[i]) % pr[i];
        return result;
    }

    public Number subtract(Number n) {
        Number result = new Number();
        for (int i = 0; i < SZ; ++i)
            result.a[i] = (a[i] - n.a[i] + pr[i]) % pr[i];
        return result;
    }

    public Number multiply(Number n) {
        Number result = new Number();
        for (int i = 0; i < SZ; ++i)
            result.a[i] = (int)((a[i] * 1l * n.a[i]) % pr[i]);
        return result;
    }

    public BigInteger bigIntegerValue(boolean can_be_negative) {
        BigInteger result = BigInteger.ZERO, mult = BigInteger.ONE;
        int x[] = new int[SZ];
        for (int i = 0; i < SZ; ++i) {
            x[i] = a[i];
            for (int j = 0; j < i; ++j) {
                long cur = (x[i] - x[j]) * 1l * r[j][i];
                x[i] = (int)((cur % pr[i] + pr[i]) % pr[i]);
            }
            result = result.add(mult.multiply(BigInteger.valueOf(x[i])));
            mult = mult.multiply(BigInteger.valueOf(pr[i]));
        }

        if (can_be_negative)
            if (result.compareTo(mult.shiftRight(1)) >= 0)
                result = result.subtract(mult);

        return result;
    }
}
```