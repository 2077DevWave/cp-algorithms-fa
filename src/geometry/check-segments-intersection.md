---
tags:
  - AI Translated
e_maxx_link: check-segments-intersection
---

# بررسی تقاطع دو پاره‌خط

دو پاره‌خط $(a, b)$ و $(c, d)$ به شما داده شده است. شما باید بررسی کنید که آیا این دو یکدیگر را قطع می‌کنند یا خیر.
البته، می‌توان نقطه‌ی تقاطع آن‌ها را پیدا کرد و بررسی نمود که تهی نباشد، اما برای پاره‌خط‌هایی با مختصات صحیح، این کار را نمی‌توان صرفاً با استفاده از اعداد صحیح انجام داد.
رویکردی که در اینجا شرح داده شده است، می‌تواند با اعداد صحیح کار کند.

## الگوریتم

ابتدا، حالتی را در نظر بگیرید که پاره‌خط‌ها روی یک خط راست قرار داشته باشند.
در این حالت، کافی است بررسی کنیم که آیا تصویر آن‌ها بر روی محورهای $Ox$ و $Oy$ با یکدیگر تقاطع دارند یا خیر.
در غیر این صورت، نقاط $a$ و $b$ نباید در یک سمتِ خط $(c, d)$ قرار گیرند، و نقاط $c$ و $d$ نیز نباید در یک سمتِ خط $(a, b)$ قرار گیرند.
این موضوع را می‌توان با چند ضرب خارجی (cross product) بررسی کرد.

## پیاده‌سازی

الگوریتم ارائه شده برای نقاط با مختصات صحیح پیاده‌سازی شده است. البته، به راحتی می‌توان آن را برای کار با اعداد double نیز تغییر داد.

```{.cpp file=check-segments-inter}
struct pt {
    long long x, y;
    pt() {}
    pt(long long _x, long long _y) : x(_x), y(_y) {}
    pt operator-(const pt& p) const { return pt(x - p.x, y - p.y); }
    long long cross(const pt& p) const { return x * p.y - y * p.x; }
    long long cross(const pt& a, const pt& b) const { return (a - *this).cross(b - *this); }
};

int sgn(const long long& x) { return x >= 0 ? x ? 1 : 0 : -1; }

bool inter1(long long a, long long b, long long c, long long d) {
    if (a > b)
        swap(a, b);
    if (c > d)
        swap(c, d);
    return max(a, c) <= min(b, d);
}

bool check_inter(const pt& a, const pt& b, const pt& c, const pt& d) {
    if (c.cross(a, d) == 0 && c.cross(b, d) == 0)
        return inter1(a.x, b.x, c.x, d.x) && inter1(a.y, b.y, c.y, d.y);
    return sgn(a.cross(b, c)) != sgn(a.cross(b, d)) &&
           sgn(c.cross(d, a)) != sgn(c.cross(d, b));
}
```