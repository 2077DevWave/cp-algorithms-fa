---
tags:
  - AI Translated
e_maxx_link: extended-euclid-algorithm
---

# الگوریتم اقلیدسی تعمیم‌یافته

در حالی که [الگوریتم اقلیدسی](euclid-algorithm.md) فقط بزرگترین مقسوم‌علیه مشترک (ب.م.م) دو عدد صحیح $a$ و $b$ را محاسبه می‌کند، نسخه تعمیم‌یافته‌ی آن راهی برای نمایش ب.م.م بر حسب $a$ و $b$ نیز پیدا می‌کند، یعنی ضرایب $x$ و $y$ را طوری می‌یابد که:

$$a \cdot x + b \cdot y = \gcd(a, b)$$

توجه به این نکته مهم است که طبق [اتحاد بزو](https://en.wikipedia.org/wiki/B%C3%A9zout%27s_identity) همیشه می‌توان چنین نمایشی را پیدا کرد. به عنوان مثال، $\gcd(55, 80) = 5$، بنابراین می‌توانیم $5$ را به صورت یک ترکیب خطی از $55$ و $80$ نمایش دهیم: $55 \cdot 3 + 80 \cdot (-2) = 5$

شکل کلی‌تری از این مسئله در مقاله [معادلات سیاله خطی](linear-diophantine-equation.md) مورد بحث قرار گرفته است. آن مقاله بر پایه این الگوریتم بنا شده است.

## الگوریتم

در این بخش، ب.م.م $a$ و $b$ را با $g$ نمایش می‌دهیم.

تغییرات نسبت به الگوریتم اصلی بسیار ساده است. اگر الگوریتم را به یاد بیاورید، می‌بینید که الگوریتم با $b = 0$ و $a = g$ به پایان می‌رسد. برای این پارامترها به راحتی می‌توان ضرایب را پیدا کرد، یعنی $g \cdot 1 + 0 \cdot 0 = g$.

با شروع از این ضرایب $(x, y) = (1, 0)$، می‌توانیم در فراخوانی‌های بازگشتی به عقب برگردیم. تنها کاری که باید انجام دهیم این است که بفهمیم ضرایب $x$ و $y$ در حین انتقال از $(a, b)$ به $(b, a \bmod b)$ چگونه تغییر می‌کنند.

فرض کنیم ضرایب $(x_1, y_1)$ را برای $(b, a \bmod b)$ پیدا کرده‌ایم:

$$b \cdot x_1 + (a \bmod b) \cdot y_1 = g$$

و می‌خواهیم زوج $(x, y)$ را برای $(a, b)$ پیدا کنیم:

$$ a \cdot x + b \cdot y = g$$

می‌توانیم $a \bmod b$ را به صورت زیر نمایش دهیم:

$$ a \bmod b = a - \left\lfloor \frac{a}{b} \right\rfloor \cdot b$$

با جایگذاری این عبارت در معادله ضرایب $(x_1, y_1)$ خواهیم داشت:

$$ g = b \cdot x_1 + (a \bmod b) \cdot y_1 = b \cdot x_1 + \left(a - \left\lfloor \frac{a}{b} \right\rfloor \cdot b \right) \cdot y_1$$

و پس از مرتب‌سازی جملات:

$$g = a \cdot y_1 + b \cdot \left( x_1 - y_1 \cdot \left\lfloor \frac{a}{b} \right\rfloor \right)$$

مقادیر $x$ و $y$ را پیدا کردیم:

$$\begin{cases}
x = y_1 \\
y = x_1 - y_1 \cdot \left\lfloor \frac{a}{b} \right\rfloor
\end{cases} $$

## پیاده‌سازی

```{.cpp file=extended_gcd}
int gcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    int x1, y1;
    int d = gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

تابع بازگشتی بالا ب.م.م و مقادیر ضرایب را به `x` و `y` (که با ارجاع به تابع پاس داده شده‌اند) برمی‌گرداند.

این پیاده‌سازی الگوریتم اقلیدسی تعمیم‌یافته برای اعداد صحیح منفی نیز نتایج صحیحی تولید می‌کند.

## نسخه تکراری

همچنین می‌توان الگوریتم اقلیدسی تعمیم‌یافته را به روش تکراری نوشت. از آنجا که این روش از بازگشت استفاده نمی‌کند، کد کمی سریع‌تر از نسخه بازگشتی اجرا می‌شود.

```{.cpp file=extended_gcd_iter}
int gcd(int a, int b, int& x, int& y) {
    x = 1, y = 0;
    int x1 = 0, y1 = 1, a1 = a, b1 = b;
    while (b1) {
        int q = a1 / b1;
        tie(x, x1) = make_tuple(x1, x - q * x1);
        tie(y, y1) = make_tuple(y1, y - q * y1);
        tie(a1, b1) = make_tuple(b1, a1 - q * b1);
    }
    return a1;
}
```

اگر با دقت به متغیرهای `a1` و `b1` نگاه کنید، متوجه می‌شوید که آنها دقیقاً همان مقادیری را می‌گیرند که در نسخه تکراری [الگوریتم اقلیدسی](euclid-algorithm.md) معمولی وجود دارد. بنابراین، الگوریتم حداقل ب.م.م صحیح را محاسبه خواهد کرد.

برای اینکه ببینیم چرا الگوریتم ضرایب صحیح را محاسبه می‌کند، در نظر بگیرید که نامتغیرهای (invariants) زیر در هر لحظه (قبل از شروع حلقه while و در پایان هر تکرار) برقرار هستند:

$$x \cdot a + y \cdot b = a_1$$

$$x_1 \cdot a + y_1 \cdot b = b_1$$

فرض کنید مقادیر در انتهای یک تکرار با یک پرایم ($'$) مشخص شوند و فرض کنید $q = \frac{a_1}{b_1}$ باشد. از [الگوریتم اقلیدسی](euclid-algorithm.md) داریم:

$$a_1' = b_1$$

$$b_1' = a_1 - q \cdot b_1$$

برای اینکه نامتغیر اول برقرار بماند، باید موارد زیر درست باشد:

$$x' \cdot a + y' \cdot b = a_1' = b_1$$

$$x' \cdot a + y' \cdot b = x_1 \cdot a + y_1 \cdot b$$

به طور مشابه برای نامتغیر دوم، باید موارد زیر برقرار باشد:

$$x_1' \cdot a + y_1' \cdot b = a_1 - q \cdot b_1$$

$$x_1' \cdot a + y_1' \cdot b = (x - q \cdot x_1) \cdot a + (y - q \cdot y_1) \cdot b$$

با مقایسه ضرایب $a$ و $b$، معادلات به‌روزرسانی برای هر متغیر به دست می‌آید و این تضمین می‌کند که نامتغیرها در طول الگوریتم حفظ می‌شوند.

در پایان می‌دانیم که $a_1$ حاوی ب.م.م است، بنابراین $x \cdot a + y \cdot b = g$. این بدان معناست که ضرایب مورد نیاز را پیدا کرده‌ایم.

حتی می‌توانید کد را بیشتر بهینه کنید و متغیرهای $a_1$ و $b_1$ را از کد حذف کرده و فقط از $a$ و $b$ دوباره استفاده کنید. با این حال، اگر این کار را انجام دهید، توانایی استدلال در مورد نامتغیرها را از دست می‌دهید.

## مسائل تمرینی

* [UVA - 10104 - مسئله اقلیدس](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1045)
* [GYM - (J) Once Upon A Time](http://codeforces.com/gym/100963)
* [UVA - 12775 - معمای هدیه](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=4628)