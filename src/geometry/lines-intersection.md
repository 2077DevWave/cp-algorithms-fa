---
tags:
  - AI Translated
e_maxx_link: lines-intersection
---

# نقطه تقاطع خطوط

دو خط با معادلات $a_1 x + b_1 y + c_1 = 0$ و $a_2 x + b_2 y + c_2 = 0$ به شما داده شده است.
باید نقطه تقاطع این دو خط را پیدا کنیم یا تشخیص دهیم که خطوط موازی هستند.

## راه‌حل

اگر دو خط موازی نباشند، یکدیگر را قطع می‌کنند.
برای یافتن نقطه تقاطع آنها، باید دستگاه معادلات خطی زیر را حل کنیم:

$$\begin{cases} a_1 x + b_1 y + c_1 = 0 \\
a_2 x + b_2 y + c_2 = 0
\end{cases}$$

با استفاده از قاعده کرامر، می‌توانیم بلافاصله جواب دستگاه را بنویسیم که نقطه تقاطع مورد نیاز خطوط را به ما می‌دهد:

$$x = - \frac{\begin{vmatrix}c_1 & b_1 \cr c_2 & b_2\end{vmatrix}}{\begin{vmatrix}a_1 & b_1 \cr a_2 & b_2\end{vmatrix} } = - \frac{c_1 b_2 - c_2 b_1}{a_1 b_2 - a_2 b_1},$$

$$y = - \frac{\begin{vmatrix}a_1 & c_1 \cr a_2 & c_2\end{vmatrix}}{\begin{vmatrix}a_1 & b_1 \cr a_2 & b_2\end{vmatrix}} = - \frac{a_1 c_2 - a_2 c_1}{a_1 b_2 - a_2 b_1}.$$

اگر مخرج برابر با $0$ باشد، یعنی:

$$\begin{vmatrix}a_1 & b_1 \cr a_2 & b_2\end{vmatrix} = a_1 b_2 - a_2 b_1 = 0 $$

در این صورت، دستگاه یا جوابی ندارد (خطوط موازی و متمایز هستند) یا بی‌نهایت جواب دارد (خطوط بر هم منطبق هستند).
اگر بخواهیم این دو حالت را از هم تشخیص دهیم، باید بررسی کنیم که آیا ضرایب $c$ با همان نسبت ضرایب $a$ و $b$ متناسب هستند یا خیر.
برای این کار کافی است دترمینان‌های زیر را محاسبه کنیم، و اگر هر دو برابر با $0$ شوند، خطوط بر هم منطبق هستند:

$$\begin{vmatrix}a_1 & c_1 \cr a_2 & c_2\end{vmatrix}, \begin{vmatrix}b_1 & c_1 \cr b_2 & c_2\end{vmatrix} $$

توجه داشته باشید که یک رویکرد متفاوت برای محاسبه نقطه تقاطع در مقاله [هندسه پایه](basic-geometry.md) توضیح داده شده است.

## پیاده‌سازی

```{.cpp file=line_intersection}
struct pt {
    double x, y;
};

struct line {
    double a, b, c;
};

const double EPS = 1e-9;

double det(double a, double b, double c, double d) {
    return a*d - b*c;
}

bool intersect(line m, line n, pt & res) {
    double zn = det(m.a, m.b, n.a, n.b);
    if (abs(zn) < EPS)
        return false;
    res.x = -det(m.c, m.b, n.c, n.b) / zn;
    res.y = -det(m.a, m.c, n.a, n.c) / zn;
    return true;
}

bool parallel(line m, line n) {
    return abs(det(m.a, m.b, n.a, n.b)) < EPS;
}

bool equivalent(line m, line n) {
    return abs(det(m.a, m.b, n.a, n.b)) < EPS
        && abs(det(m.a, m.c, n.a, n.c)) < EPS
        && abs(det(m.b, m.c, n.b, n.c)) < EPS;
}
```