---
tags:
  - AI Translated
e_maxx_link: mex
---

# MEX (کوچکترین عضو ناموجود) یک دنباله

با داشتن آرایه $A$ به طول $N$، باید کوچکترین عدد صحیح غیرمنفی را که در آرایه وجود ندارد، پیدا کنید. این عدد معمولاً **MEX** (کوچکترین عضو حذف‌شده یا minimal excluded) نامیده می‌شود.

$$
\begin{align}
\text{mex}(\{0, 1, 2, 4, 5\}) &= 3 \\
\text{mex}(\{0, 1, 2, 3, 4\}) &= 5 \\
\text{mex}(\{1, 2, 3, 4, 5\}) &= 0 \\
\end{align}
$$

توجه داشته باشید که MEX یک آرایه به طول $N$ هرگز نمی‌تواند بزرگتر از خود $N$ باشد.

ساده‌ترین رویکرد این است که یک `set` از تمام عناصر آرایه $A$ بسازیم تا بتوانیم به سرعت بررسی کنیم که آیا عددی در آرایه وجود دارد یا خیر.
سپس می‌توانیم تمام اعداد از $0$ تا $N$ را بررسی کنیم و اگر عدد فعلی در `set` وجود نداشت، آن را برگردانیم.

## پیاده‌سازی

الگوریتم زیر در زمان $O(N \log N)$ اجرا می‌شود.

```{.cpp file=mex_simple}
int mex(vector<int> const& A) {
    set<int> b(A.begin(), A.end());

    int result = 0;
    while (b.count(result))
        ++result;
    return result;
}
```

اگر یک الگوریتم به محاسبه MEX با پیچیدگی زمانی $O(N)$ نیاز داشته باشد، می‌توان به جای `set` از یک آرایه بولین (boolean) استفاده کرد.
توجه داشته باشید که اندازه این آرایه کمکی باید به اندازه بزرگترین مقدار ممکن در آرایه باشد.


```{.cpp file=mex_linear}
int mex(vector<int> const& A) {
    static bool used[MAX_N+1] = { 0 };

    // علامت‌گذاری اعداد موجود
    for (int x : A) {
        if (x <= MAX_N)
            used[x] = true;
    }

    // پیدا کردن mex
    int result = 0;
    while (used[result])
        ++result;
 
    // پاک‌سازی دوباره آرایه کمکی
    for (int x : A) {
        if (x <= MAX_N)
            used[x] = false;
    }

    return result;
}
```

این رویکرد سریع است، اما تنها زمانی خوب کار می‌کند که لازم باشد MEX را فقط یک بار محاسبه کنید.
اگر نیاز به محاسبه مکرر MEX داشته باشید، برای مثال به این دلیل که آرایه شما مدام در حال تغییر است، این روش کارآمد نخواهد بود.
برای این منظور، به راهکار بهتری نیاز داریم.

## محاسبه MEX با به‌روزرسانی‌های آرایه

در این نوع مسائل، شما باید اعداد خاصی را در آرایه تغییر دهید و پس از هر به‌روزرسانی، MEX جدید آرایه را محاسبه کنید.

برای این کار به یک ساختمان داده بهتر نیاز است تا بتواند چنین پرس‌وجوهایی را به صورت کارآمد مدیریت کند.

یک رویکرد این است که فراوانی هر عدد از $0$ تا $N$ را محاسبه کرده و یک ساختمان داده درختی روی آن بسازیم. به عنوان مثال، یک درخت بازه‌ای (segment tree) یا یک treap. هر گره نماینده یک بازه از اعداد است و علاوه بر مجموع فراوانی در آن بازه، تعداد اعداد متمایز موجود در آن بازه را نیز ذخیره می‌کند. به‌روزرسانی این ساختمان داده در زمان $O(\log N)$ ممکن است و همچنین می‌توان MEX را با انجام یک جستجوی دودویی روی درخت در زمان $O(\log N)$ پیدا کرد. اگر گره‌ای که نماینده بازه $[0, \lfloor N/2 \rfloor)$ است، شامل $\lfloor N/2 \rfloor$ عدد متمایز نباشد، پس یک عدد در این بازه وجود ندارد و MEX کوچکتر از $\lfloor N/2 \rfloor$ است و می‌توان به صورت بازگشتی در شاخه چپ درخت جستجو کرد. در غیر این صورت، MEX حداقل برابر با $\lfloor N/2 \rfloor$ است و می‌توان در شاخه راست درخت به صورت بازگشتی جستجو کرد.

همچنین می‌توان از ساختمان داده‌های کتابخانه استاندارد مانند `map` و `set` استفاده کرد (بر اساس رویکردی که [اینجا](https://codeforces.com/blog/entry/81287?#comment-677837) توضیح داده شده است).
با یک `map` فراوانی هر عدد را ذخیره می‌کنیم و با یک `set`، اعدادی را که در حال حاضر در آرایه موجود نیستند، نگهداری می‌کنیم.
از آنجایی که `set` مرتب است، `*set.begin()` همان MEX خواهد بود.
در مجموع به پیش‌پردازشی با پیچیدگی زمانی $O(N \log N)$ نیاز داریم و پس از آن، MEX را می‌توان در زمان $O(1)$ محاسبه کرد و هر به‌روزرسانی را در زمان $O(\log N)$ انجام داد.

```{.cpp file=mex_updates}
class Mex {
private:
    map<int, int> frequency;
    set<int> missing_numbers;
    vector<int> A;

public:
    Mex(vector<int> const& A) : A(A) {
        for (int i = 0; i <= A.size(); i++)
            missing_numbers.insert(i);

        for (int x : A) {
            ++frequency[x];
            missing_numbers.erase(x);
        }
    }

    int mex() {
        return *missing_numbers.begin();
    }

    void update(int idx, int new_value) {
        if (--frequency[A[idx]] == 0)
            missing_numbers.insert(A[idx]);
        A[idx] = new_value;
        ++frequency[new_value];
        missing_numbers.erase(new_value);
    }
};
```

## مسائل تمرینی

- [AtCoder: Neq Min](https://atcoder.jp/contests/hhkb2020/tasks/hhkb2020_c)
- [Codeforces: Informatics in MAC](https://codeforces.com/contest/1935/problem/B)
- [Codeforces: Replace by MEX](https://codeforces.com/contest/1375/problem/D)
- [Codeforces: Vitya and Strange Lesson](https://codeforces.com/problemset/problem/842/D)
- [Codeforces: MEX Queries](https://codeforces.com/contest/817/problem/F)