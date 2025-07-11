---
tags:
  - AI Translated
e_maxx_link: zero_matrix
---

# پیدا کردن بزرگترین زیرماتریس صفر

ماتریسی با `n` سطر و `m` ستون به شما داده می‌شود. بزرگترین زیرماتریس متشکل از فقط صفر را پیدا کنید (یک زیرماتریس، ناحیه‌ای مستطیلی از ماتریس است).

## الگوریتم

عناصر ماتریس `a[i][j]` خواهند بود، که در آن `i = 0...n - 1` و `j = 0... m - 1` است. برای سادگی، تمام عناصر غیرصفر را برابر با ۱ در نظر می‌گیریم.

### مرحله ۱: دینامیک کمکی

ابتدا، ماتریس کمکی زیر را محاسبه می‌کنیم: `d[i][j]`، که شماره سطر نزدیک‌ترین `۱` بالای `a[i][j]` است. به بیان رسمی، `d[i][j]` بزرگترین شماره سطر (از `0` تا `i - 1`) است که در ستون `j`-ام آن، عنصری برابر با `۱` وجود دارد.
هنگام پیمایش از بالا-چپ به پایین-راست، وقتی در سطر `i` هستیم، مقادیر سطر قبلی را می‌دانیم، بنابراین کافی است فقط عناصری را که مقدار `۱` دارند به‌روزرسانی کنیم. می‌توانیم این مقادیر را در یک آرایه ساده `d` به طول `m` ذخیره کنیم، زیرا در ادامه الگوریتم، ماتریس را سطر به سطر پردازش خواهیم کرد و فقط به مقادیر مربوط به سطر فعلی نیاز داریم.

```cpp
vector<int> d(m, -1);
for (int i = 0; i < n; ++i) {
    for (int j = 0; j < m; ++j) {
        if (a[i][j] == 1) {
            d[j] = i;
        }
    }
}
```

### مرحله ۲: حل مسئله

می‌توانیم مسئله را با پیمایش سطرها و در نظر گرفتن تمام ستون‌های چپ و راست ممکن برای یک زیرماتریس، در زمان $O(n m^2)$ حل کنیم. پایین مستطیل، سطر فعلی خواهد بود و با استفاده از `d[i][j]` می‌توانیم سطر بالایی را پیدا کنیم. با این حال، می‌توان پا را فراتر گذاشت و پیچیدگی راه‌حل را به طور قابل توجهی بهبود بخشید.

واضح است که زیرماتریس صفر مورد نظر از هر چهار طرف توسط تعدادی عدد یک محدود شده است که از افزایش اندازه و بهبود پاسخ جلوگیری می‌کنند. بنابراین، اگر به صورت زیر عمل کنیم، پاسخ را از دست نخواهیم داد: برای هر سلول `j` در سطر `i` (سطر پایینی یک زیرماتریس صفر بالقوه)، `d[i][j]` را به عنوان سطر بالایی زیرماتریس صفر فعلی در نظر می‌گیریم. اکنون باید مرزهای چپ و راست بهینه زیرماتریس صفر را تعیین کنیم، یعنی این زیرماتریس را تا حد امکان به سمت چپ و راست ستون `j`-ام گسترش دهیم.

گسترش حداکثری به سمت چپ به چه معناست؟ یعنی پیدا کردن اندیس `k1` که برای آن `d[i][k1] > d[i][j]` باشد و همزمان `k1` نزدیک‌ترین اندیس در سمت چپ `j` باشد. واضح است که در این صورت، `k1 + 1` شماره ستون چپ زیرماتریس صفر مورد نظر را می‌دهد. اگر چنین اندیسی اصلاً وجود نداشته باشد، `k1` را برابر با `-1` قرار می‌دهیم (این به این معنی است که توانسته‌ایم زیرماتریس صفر فعلی را از سمت چپ تا مرز ماتریس `a` گسترش دهیم).

به طور متقارن، می‌توان اندیس `k2` را برای مرز راست تعریف کرد: این نزدیک‌ترین اندیس در سمت راست `j` است که `d[i][k2] > d[i][j]` باشد (یا `m`، اگر چنین اندیسی وجود نداشته باشد).

بنابراین، اندیس‌های `k1` و `k2`، اگر یاد بگیریم آن‌ها را به طور مؤثر جستجو کنیم، تمام اطلاعات لازم را در مورد زیرماتریس صفر فعلی به ما می‌دهند. به طور خاص، مساحت آن برابر با `(i - d[i][j]) * (k2 - k1 - 1)` خواهد بود.

چگونه می‌توان این اندیس‌های `k1` و `k2` را با `i` و `j` ثابت، به طور مؤثر جستجو کرد؟ می‌توانیم این کار را به طور متوسط در زمان $O(1)$ انجام دهیم.

برای رسیدن به چنین پیچیدگی‌ای، می‌توان از پشته (stack) به صورت زیر استفاده کرد. ابتدا یاد می‌گیریم که چگونه اندیس `k1` را پیدا کنیم و مقدار آن را برای هر اندیس `j` در سطر فعلی `i` در آرایه `d1[i][j]` ذخیره کنیم. برای این کار، تمام ستون‌های `j` را از چپ به راست مرور می‌کنیم و پشته‌ای از اندیس‌ها را نگهداری می‌کنیم. هنگام حرکت از یک ستون `j` به ستون بعدی، لازم است محتوای پشته را به‌روزرسانی کنیم. وقتی یک عنصر نامناسب در بالای پشته وجود دارد (یعنی مقدار `d` آن `<=` مقدار `d[i][j]` است)، آن را pop می‌کنیم. به راحتی می‌توان فهمید که کافی است عناصر را فقط از بالای پشته حذف کنیم و نه از جای دیگر (زیرا پشته شامل دنباله‌ای از ستون‌ها با مقادیر `d` صعودی خواهد بود).

مقدار `d1[i][j]` برای هر `j` برابر با مقداری خواهد بود که در آن لحظه در بالای پشته قرار دارد.

دینامیک `d2[i][j]` برای پیدا کردن اندیس‌های `k2` مشابه در نظر گرفته می‌شود، فقط باید ستون‌ها را از راست به چپ مرور کنید.

واضح است از آنجایی که در هر سطر دقیقاً `m` عنصر به پشته اضافه می‌شود، نمی‌تواند تعداد حذف‌ها بیشتر از آن باشد. بنابراین، مجموع پیچیدگی‌ها خطی خواهد بود، و در نتیجه پیچیدگی نهایی الگوریتم $O(nm)$ است.

همچنین باید توجه داشت که این الگوریتم (بدون در نظر گرفتن داده‌های ورودی - ماتریس `a[][]`) از حافظه $O(m)$ استفاده می‌کند.

### پیاده‌سازی

```cpp
int zero_matrix(vector<vector<int>> a) {
    int n = a.size();
    int m = a[0].size();

    int ans = 0;
    vector<int> d(m, -1), d1(m), d2(m);
    stack<int> st;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            if (a[i][j] == 1)
                d[j] = i;
        }

        for (int j = 0; j < m; ++j) {
            while (!st.empty() && d[st.top()] <= d[j])
                st.pop();
            d1[j] = st.empty() ? -1 : st.top();
            st.push(j);
        }
        while (!st.empty())
            st.pop();

        for (int j = m - 1; j >= 0; --j) {
            while (!st.empty() && d[st.top()] <= d[j])
                st.pop();
            d2[j] = st.empty() ? m : st.top();
            st.push(j);
        }
        while (!st.empty())
            st.pop();

        for (int j = 0; j < m; ++j)
            ans = max(ans, (i - d[j]) * (d2[j] - d1[j] - 1));
    }
    return ans;
}
```