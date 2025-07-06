---
tags:
  - AI Translated
e_maxx_link: circle-circle-intersection
---

# تقاطع دو دایره

دو دایره در یک صفحه دوبعدی به شما داده شده است که هر کدام با مختصات مرکز و شعاع آن مشخص شده‌اند. نقاط تقاطع آن‌ها را پیدا کنید (حالات ممکن: یک یا دو نقطه، عدم تقاطع یا انطباق کامل دایره‌ها).

## راه‌حل

بیایید این مسئله را به [مسئله تقاطع دایره و خط](circle-line-intersection.md) کاهش دهیم.

بدون از دست دادن کلیت مسئله، فرض کنید دایره اول در مبدأ مختصات قرار دارد (اگر اینطور نباشد، می‌توانیم مبدأ را به مرکز دایره اول منتقل کرده و در زمان خروجی، مختصات نقاط تقاطع را متناسب با آن تنظیم کنیم). در این صورت، یک دستگاه دو معادله‌ای داریم:

$$x^2+y^2=r_1^2$$

$$(x - x_2)^2 + (y - y_2)^2 = r_2^2$$

معادله اول را از دومی کم می‌کنیم تا از توان‌های دوم متغیرها خلاص شویم:

$$x^2+y^2=r_1^2$$

$$x \cdot (-2x_2) + y \cdot (-2y_2) + (x_2^2+y_2^2+r_1^2-r_2^2) = 0$$

به این ترتیب، مسئله اصلی را به مسئله پیدا کردن تقاطع‌های دایره اول و یک خط کاهش دادیم:

$$Ax + By + C = 0$$

$$\begin{align}
A &= -2x_2 \\
B &= -2y_2 \\
C &= x_2^2+y_2^2+r_1^2-r_2^2
\end{align}$$

و این مسئله را می‌توان همانطور که در [مقاله مربوطه](circle-line-intersection.md) توضیح داده شده است، حل کرد.

تنها حالت خاصی که باید جداگانه در نظر بگیریم، زمانی است که مراکز دایره‌ها بر هم منطبق باشند. در این حالت $x_2=y_2=0$ و معادله خط به صورت $C = r_1^2-r_2^2 = 0$ خواهد بود. اگر شعاع دایره‌ها یکسان باشد، بی‌نهایت نقطه تقاطع وجود دارد و اگر متفاوت باشند، هیچ تقاطعی وجود نخواهد داشت.

## مسائل تمرینی

- [RadarFinder](https://community.topcoder.com/stat?c=problem_statement&pm=7766)
- [Runaway to a shadow - Codeforces Round #357](http://codeforces.com/problemset/problem/681/E)
- [ASC 1 Problem F "Get out!"](http://codeforces.com/gym/100199/problem/F)
- [SPOJ: CIRCINT](http://www.spoj.com/problems/CIRCINT/)
- [UVA - 10301 - Rings and Glue](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1242)
- [Codeforces 933C A Colorful Prospect](https://codeforces.com/problemset/problem/933/C)
- [TIMUS 1429 Biscuits](https://acm.timus.ru/problem.aspx?space=1&num=1429)