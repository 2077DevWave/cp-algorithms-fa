---
tags:
  - AI Translated
e_maxx_link: segments-intersection
---

# یافتن نقطه تقاطع دو پاره‌خط

دو پاره‌خط AB و CD به شما داده شده است که با زوج نقاط انتهایی‌شان مشخص شده‌اند. هر پاره‌خط می‌تواند یک نقطه باشد اگر نقاط انتهایی آن یکسان باشند. شما باید اشتراک این دو پاره‌خط را پیدا کنید، که می‌تواند تهی (اگر پاره‌خط‌ها تقاطع نداشته باشند)، یک نقطه، یا یک پاره‌خط دیگر (اگر پاره‌خط‌های داده‌شده هم‌پوشانی داشته باشند) باشد.

## راه‌حل

می‌توانیم نقطه تقاطع پاره‌خط‌ها را همانند [تقاطع خطوط](lines-intersection.md) پیدا کنیم:
معادلات خط را از نقاط انتهایی پاره‌خط‌ها بازسازی کرده و موازی بودن آن‌ها را بررسی می‌کنیم.

اگر خطوط موازی نباشند، باید نقطه تقاطع آن‌ها را پیدا کرده و بررسی کنیم که آیا این نقطه به هر دو پاره‌خط تعلق دارد یا خیر (برای این کار کافی است بررسی کنیم که نقطه تقاطع به تصویر هر پاره‌خط روی محورهای X و Y تعلق داشته باشد). در این حالت، پاسخ یا "عدم تقاطع" است یا همان نقطه تقاطع خطوط.

حالت خطوط موازی کمی پیچیده‌تر است (حالت یکی یا هر دو پاره‌خط بودن به صورت یک نقطه نیز در این دسته قرار می‌گیرد). در این حالت، باید بررسی کنیم که هر دو پاره‌خط روی یک خط قرار دارند یا خیر. اگر اینطور نباشد، پاسخ "عدم تقاطع" است. اگر روی یک خط باشند، پاسخ برابر است با اشتراک دو پاره‌خطِ هم‌خط. این اشتراک با مرتب‌سازی نقاط انتهایی هر دو پاره‌خط بر اساس یک مختصات مشخص به ترتیب صعودی و سپس در نظر گرفتن راست‌ترین نقطه از میان نقاط ابتدایی و چپ‌ترین نقطه از میان نقاط انتهایی به دست می‌آید.

اگر هر دو پاره‌خط تک‌نقطه‌ای باشند، این نقاط باید یکسان باشند و منطقی است که این بررسی به طور جداگانه انجام شود.

در ابتدای الگوریتم، یک بررسی جعبه مرزی (Bounding Box Check) اضافه می‌کنیم - این بررسی برای حالتی که پاره‌خط‌ها روی یک خط قرار دارند ضروری است و (به دلیل سبک بودن) باعث می‌شود الگوریتم به طور میانگین در تست‌های تصادفی سریع‌تر عمل کند.

## پیاده‌سازی

در ادامه پیاده‌سازی این الگوریتم، شامل تمام توابع کمکی برای پردازش خطوط و پاره‌خط‌ها، آمده است.

تابع اصلی `intersect` در صورتی که پاره‌خط‌ها اشتراک غیرتهی داشته باشند، `true` برمی‌گرداند و نقاط انتهایی پاره‌خط اشتراک را در آرگومان‌های `left` و `right` ذخیره می‌کند. اگر پاسخ یک نقطه باشد، مقادیری که در `left` و `right` نوشته می‌شوند یکسان خواهند بود.

```{.cpp file=segment_intersection}
const double EPS = 1E-9;

struct pt {
    double x, y;

    bool operator<(const pt& p) const
    {
        return x < p.x - EPS || (abs(x - p.x) < EPS && y < p.y - EPS);
    }
};

struct line {
    double a, b, c;

    line() {}
    line(pt p, pt q)
    {
        a = p.y - q.y;
        b = q.x - p.x;
        c = -a * p.x - b * p.y;
        norm();
    }

    void norm()
    {
        double z = sqrt(a * a + b * b);
        if (abs(z) > EPS)
            a /= z, b /= z, c /= z;
    }

    double dist(pt p) const { return a * p.x + b * p.y + c; }
};

double det(double a, double b, double c, double d)
{
    return a * d - b * c;
}

inline bool betw(double l, double r, double x)
{
    return min(l, r) <= x + EPS && x <= max(l, r) + EPS;
}

inline bool intersect_1d(double a, double b, double c, double d)
{
    if (a > b)
        swap(a, b);
    if (c > d)
        swap(c, d);
    return max(a, c) <= min(b, d) + EPS;
}

bool intersect(pt a, pt b, pt c, pt d, pt& left, pt& right)
{
    if (!intersect_1d(a.x, b.x, c.x, d.x) || !intersect_1d(a.y, b.y, c.y, d.y))
        return false;
    line m(a, b);
    line n(c, d);
    double zn = det(m.a, m.b, n.a, n.b);
    if (abs(zn) < EPS) {
        if (abs(m.dist(c)) > EPS || abs(n.dist(a)) > EPS)
            return false;
        if (b < a)
            swap(a, b);
        if (d < c)
            swap(c, d);
        left = max(a, c);
        right = min(b, d);
        return true;
    } else {
        left.x = right.x = -det(m.c, m.b, n.c, n.b) / zn;
        left.y = right.y = -det(m.a, m.c, n.a, n.c) / zn;
        return betw(a.x, b.x, left.x) && betw(a.y, b.y, left.y) &&
               betw(c.x, d.x, left.x) && betw(c.y, d.y, left.y);
    }
}
```