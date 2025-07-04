---
tags:
  - AI Translated
e_maxx_link: k-th
---

# $K$-امین آمارۀ ترتیبی در زمان $O(N)$

یک آرایۀ $A$ به اندازۀ $N$ و یک عدد $K$ داده شده است. مسئله پیدا کردن $K$-امین عدد بزرگ آرایه است، یعنی $K$-امین آمارۀ ترتیبی.

ایده اصلی، استفاده از ایدۀ الگوریتم quick sort است. در واقع، الگوریتم ساده است؛ اثبات اینکه به طور متوسط در زمان $O(N)$ اجرا می‌شود، در مقایسه با quick sort، دشوارتر است.

## پیاده‌سازی (غیر بازگشتی)

```cpp
template <class T>
T order_statistics (std::vector<T> a, unsigned n, unsigned k)
{
    using std::swap;
    for (unsigned l=1, r=n; ; )
    {
        if (r <= l+1)
        {
            // اندازۀ قسمت فعلی ۱ یا ۲ است، پس پیدا کردن جواب آسان است
            if (r == l+1 && a[r] < a[l])
                swap (a[l], a[r]);
            return a[k];
        }

        // مرتب‌سازی a[l]، a[l+1]، a[r]
        unsigned mid = (l + r) >> 1;
        swap (a[mid], a[l+1]);
        if (a[l] > a[r])
            swap (a[l], a[r]);
        if (a[l+1] > a[r])
            swap (a[l+1], a[r]);
        if (a[l] > a[l+1])
            swap (a[l], a[l+1]);

        // انجام تقسیم
        // عنصر محوری a[l + 1] است، یعنی میانه در بین a[l]، a[l + 1]، a[r]
        unsigned
            i = l+1,
            j = r;
        const T
            cur = a[l+1];
        for (;;)
        {
            while (a[++i] < cur) ;
            while (a[--j] > cur) ;
            if (i > j)
                break;
            swap (a[i], a[j]);
        }

        // قرار دادن عنصر محوری
        a[l+1] = a[j];
        a[j] = cur;
        
        // کار را در قسمتی ادامه می‌دهیم که حتماً شامل عنصر مورد نظر است
        if (j >= k)
            r = j-1;
        if (j <= k)
            l = i;
    }
}
```

## نکات
* الگوریتم تصادفی بالا [quickselect](https://en.wikipedia.org/wiki/Quickselect) نام دارد. برای اجرای صحیح، باید قبل از فراخوانی آن، آرایۀ $A$ را به صورت تصادفی بر بزنید یا از یک عنصر تصادفی به عنوان عنصر محوری استفاده کنید. الگوریتم‌های قطعی نیز وجود دارند که مسئله را در زمان خطی حل می‌کنند، مانند [median of medians](https://en.wikipedia.org/wiki/Median_of_medians).
* تابع [std::nth_element](https://en.cppreference.com/w/cpp/algorithm/nth_element) این مسئله را در C++ حل می‌کند، اما پیاده‌سازی آن در gcc در بدترین حالت در زمان $O(n \log n)$ اجرا می‌شود.
* پیدا کردن $K$ کوچک‌ترین عنصر را می‌توان با یک سربار خطی به مسئله پیدا کردن عنصر $K$-ام کاهش داد، زیرا این عناصر دقیقاً همان‌هایی هستند که از عنصر $K$-ام کوچک‌ترند.

## مسائل تمرینی
- [Leetcode: Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)
- [CODECHEF: Median](https://www.codechef.com/problems/CD1IT1)