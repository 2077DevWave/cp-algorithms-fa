---
tags:
  - AI Translated
e_maxx_link: point-in-convex-polygon
---

# بررسی تعلق یک نقطه به چندضلعی محدب در $O(\log N)$

مسئله زیر را در نظر بگیرید: یک چندضلعی محدب با رئوس صحیح و تعداد زیادی پرس‌وجو (query) به شما داده شده است. هر پرس‌وجو یک نقطه است که باید تعیین کنیم آیا درون چندضلعی یا روی مرز آن قرار دارد یا خیر. فرض کنید رئوس چندضلعی به ترتیب پادساعتگرد مرتب شده‌اند. ما به هر پرس‌وجو به صورت آنلاین و در زمان $O(\log n)$ پاسخ خواهیم داد.

## الگوریتم
نقطه‌ای با کوچکترین مختص x را انتخاب می‌کنیم. اگر چند نقطه با این ویژگی وجود داشت، نقطه‌ای را انتخاب می‌کنیم که کوچکترین مختص y را داشته باشد. این نقطه را $p_0$ می‌نامیم.
حال، تمام نقاط دیگر چندضلعی، یعنی $p_1,\dots,p_n$، بر اساس زاویه قطبی‌شان نسبت به نقطه انتخابی مرتب شده‌اند (زیرا چندضلعی به ترتیب پادساعتگرد مرتب شده است).

اگر نقطه مورد نظر متعلق به چندضلعی باشد، متعلق به یک مثلث $p_0, p_i, p_{i + 1}$ خواهد بود (و اگر روی مرز مشترک مثلث‌ها قرار داشته باشد، ممکن است متعلق به بیش از یک مثلث باشد).
مثلث $p_0, p_i, p_{i + 1}$ را در نظر بگیرید به طوری که نقطه $p$ به این مثلث تعلق داشته باشد و $i$ در میان تمام چنین مثلث‌هایی بیشترین مقدار ممکن باشد.

یک حالت خاص وجود دارد که $p$ روی پاره‌خط $(p_0, p_n)$ قرار می‌گیرد. این حالت را جداگانه بررسی خواهیم کرد.
در غیر این صورت، تمام نقاط $p_j$ با $j \le i$ نسبت به $p_0$ در جهت پادساعتگرد از $p$ قرار دارند، و تمام نقاط دیگر این‌طور نیستند.
این بدان معناست که می‌توانیم با جستجوی دودویی، نقطه‌ی $p_i$ را پیدا کنیم به طوری که $p_i$ نسبت به $p_0$ در جهت پادساعتگرد از $p$ قرار *نداشته باشد* و $i$ در میان تمام چنین نقاطی بیشترین مقدار باشد.
و پس از آن، بررسی می‌کنیم که آیا نقطه واقعاً در مثلث تعیین‌شده قرار دارد یا خیر.

علامت $(a - c) \times (b - c)$ به ما می‌گوید که آیا نقطه $a$ نسبت به نقطه $b$ حول نقطه $c$، در جهت ساعتگرد است یا پادساعتگرد.
اگر $(a - c) \times (b - c) > 0$ باشد، آنگاه نقطه $a$ در سمت راست برداری قرار دارد که از $c$ به $b$ می‌رود، که به معنای ساعتگرد بودن آن نسبت به $b$ حول $c$ است.
و اگر $(a - c) \times (b - c) < 0$ باشد، آنگاه نقطه در سمت چپ، یا پادساعتگرد قرار دارد.
و اگر حاصلضرب صفر باشد، نقطه دقیقاً روی خطی است که از نقاط $b$ و $c$ می‌گذرد.

بازگردیم به الگوریتم:
نقطه پرس‌وجو $p$ را در نظر بگیرید.
ابتدا باید بررسی کنیم که آیا نقطه بین $p_1$ و $p_n$ قرار می‌گیرد.
در غیر این صورت از قبل می‌دانیم که نمی‌تواند بخشی از چندضلعی باشد.
این کار را می‌توان با بررسی این موضوع انجام داد که آیا حاصلضرب خارجی $(p_1 - p_0)\times(p - p_0)$ صفر است یا علامتی یکسان با $(p_1 - p_0)\times(p_n - p_0)$ دارد، و همچنین آیا $(p_n - p_0)\times(p - p_0)$ صفر است یا علامتی یکسان با $(p_n - p_0)\times(p_1 - p_0)$ دارد.
سپس حالت خاصی را که در آن $p$ بخشی از خط $(p_0, p_1)$ است، مدیریت می‌کنیم.
و سپس می‌توانیم با جستجوی دودویی، آخرین نقطه از میان $p_1,\dots p_n$ را پیدا کنیم که نسبت به $p_0$ در جهت پادساعتگرد از $p$ قرار *ندارد*.
برای یک نقطه منفرد $p_i$ این شرط را می‌توان با بررسی $(p_i - p_0)\times(p - p_0) \le 0$ انجام داد. پس از یافتن چنین نقطه‌ای $p_i$، باید بررسی کنیم که آیا $p$ درون مثلث $p_0, p_i, p_{i + 1}$ قرار دارد یا خیر.
برای آزمایش تعلق نقطه به مثلث، می‌توانیم به سادگی بررسی کنیم که آیا $|(p_i - p_0)\times(p_{i + 1} - p_0)| = |(p_0 - p)\times(p_i - p)| + |(p_i - p)\times(p_{i + 1} - p)| + |(p_{i + 1} - p)\times(p_0 - p)|$ برقرار است.
این رابطه بررسی می‌کند که آیا مساحت مثلث $p_0, p_i, p_{i+1}$ دقیقاً با مجموع مساحت‌های سه مثلث $p, p_0, p_i$ و $p, p_i, p_{i+1}$ و $p, p_{i+1}, p_0$ برابر است.
اگر $p$ بیرون باشد، مجموع مساحت آن سه مثلث از مساحت مثلث اصلی بزرگتر خواهد بود. اگر داخل باشد، برابر خواهد بود.

## پیاده‌سازی

تابع `prepare` تضمین می‌کند که نقطه‌ی با کوچکترین مقدار لغوی (کوچکترین مقدار x، و در صورت تساوی، کوچکترین مقدار y) به عنوان $p_0$ انتخاب شود و بردارهای $p_i - p_0$ را محاسبه می‌کند.
پس از آن، تابع `pointInConvexPolygon` نتیجه یک پرس‌وجو را محاسبه می‌کند.
ما همچنین نقطه $p_0$ را به خاطر می‌سپاریم و تمام نقاط پرس‌وجو شده را با آن انتقال (translate) می‌دهیم تا فاصله صحیح را محاسبه کنیم، زیرا بردارها نقطه شروع مشخصی ندارند.
با انتقال نقاط پرس‌وجو، می‌توانیم فرض کنیم که تمام بردارها از مبدأ $(0, 0)$ شروع می‌شوند و محاسبات مربوط به فاصله‌ها و طول‌ها را ساده‌تر کنیم.

```{.cpp file=points_in_convex_polygon}
struct pt {
    long long x, y;
    pt() {}
    pt(long long _x, long long _y) : x(_x), y(_y) {}
    pt operator+(const pt &p) const { return pt(x + p.x, y + p.y); }
    pt operator-(const pt &p) const { return pt(x - p.x, y - p.y); }
    long long cross(const pt &p) const { return x * p.y - y * p.x; }
    long long dot(const pt &p) const { return x * p.x + y * p.y; }
    long long cross(const pt &a, const pt &b) const { return (a - *this).cross(b - *this); }
    long long dot(const pt &a, const pt &b) const { return (a - *this).dot(b - *this); }
    long long sqrLen() const { return this->dot(*this); }
};

bool lexComp(const pt &l, const pt &r) {
    return l.x < r.x || (l.x == r.x && l.y < r.y);
}

int sgn(long long val) { return val > 0 ? 1 : (val == 0 ? 0 : -1); }

vector<pt> seq;
pt translation;
int n;

bool pointInTriangle(pt a, pt b, pt c, pt point) {
    long long s1 = abs(a.cross(b, c));
    long long s2 = abs(point.cross(a, b)) + abs(point.cross(b, c)) + abs(point.cross(c, a));
    return s1 == s2;
}

void prepare(vector<pt> &points) {
    n = points.size();
    int pos = 0;
    for (int i = 1; i < n; i++) {
        if (lexComp(points[i], points[pos]))
            pos = i;
    }
    rotate(points.begin(), points.begin() + pos, points.end());

    n--;
    seq.resize(n);
    for (int i = 0; i < n; i++)
        seq[i] = points[i + 1] - points[0];
    translation = points[0];
}

bool pointInConvexPolygon(pt point) {
    point = point - translation;
    if (seq[0].cross(point) != 0 &&
            sgn(seq[0].cross(point)) != sgn(seq[0].cross(seq[n - 1])))
        return false;
    if (seq[n - 1].cross(point) != 0 &&
            sgn(seq[n - 1].cross(point)) != sgn(seq[n - 1].cross(seq[0])))
        return false;

    if (seq[0].cross(point) == 0)
        return seq[0].sqrLen() >= point.sqrLen();

    int l = 0, r = n - 1;
    while (r - l > 1) {
        int mid = (l + r) / 2;
        int pos = mid;
        if (seq[pos].cross(point) >= 0)
            l = mid;
        else
            r = mid;
    }
    int pos = l;
    return pointInTriangle(seq[pos], seq[pos + 1], pt(0, 0), point);
}
```

## مسائل
[SGU253 Theodore Roosevelt](https://codeforces.com/problemsets/acmsguru/problem/99999/253)
[Codeforces 55E Very simple problem](https://codeforces.com/contest/55/problem/E)