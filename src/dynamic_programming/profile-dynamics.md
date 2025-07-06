---
tags:
  - AI Translated
e_maxx_link: profile-dynamics
---

# برنامه‌ریزی پویا روی پروفایل شکسته. مسئله «پارکت»

مسائل رایجی که با استفاده از DP روی پروفایل شکسته حل می‌شوند، عبارتند از:

- پیدا کردن تعداد راه‌های پر کردن کامل یک ناحیه (مثلاً صفحه شطرنج/جدول) با اشکال مشخص (مثلاً دومینو)
- پیدا کردن راهی برای پر کردن یک ناحیه با کمترین تعداد شکل
- پیدا کردن یک چیدمان جزئی با کمترین فضای خالی (یا خانه‌های خالی، در مورد جدول)
- پیدا کردن یک چیدمان جزئی با کمترین تعداد شکل، به طوری که دیگر نتوان شکلی به آن اضافه کرد

## مسئله «پارکت»

**شرح مسئله.** جدولی با ابعاد $N \times M$ داده شده است. تعداد راه‌های پر کردن این جدول با اشکال $2 \times 1$ را پیدا کنید (هیچ خانه‌ای نباید خالی بماند و اشکال نباید با یکدیگر همپوشانی داشته باشند).

حالت DP را به صورت $dp[i, mask]$ تعریف می‌کنیم، که در آن $i = 1, \ldots N$ و $mask = 0, \ldots 2^M - 1$ است.

$i$ تعداد سطرهای جدول فعلی را نشان می‌دهد، و $mask$ وضعیت سطر آخر جدول فعلی است. اگر بیت $j$-ام از $mask$ برابر $0$ باشد، خانه متناظر آن پر شده است، در غیر این صورت خالی است.

بدیهی است که پاسخ مسئله برابر با $dp[N, 0]$ خواهد بود.

ما حالت DP را با پیمایش روی هر $i = 1, \cdots N$ و هر $mask = 0, \ldots 2^M - 1$ می‌سازیم، و برای هر $mask$ فقط به جلو انتقال (transition) انجام می‌دهیم، یعنی اشکال را به جدول فعلی _اضافه_ می‌کنیم.

### پیاده‌سازی

```cpp
int n, m;
vector < vector<long long> > dp;


void calc (int x = 0, int y = 0, int mask = 0, int next_mask = 0)
{
	if (x == n)
		return;
	if (y >= m)
		dp[x+1][next_mask] += dp[x][mask];
	else
	{
		int my_mask = 1 << y;
		if (mask & my_mask)
			calc (x, y+1, mask, next_mask);
		else
		{
			calc (x, y+1, mask, next_mask | my_mask);
			if (y+1 < m && ! (mask & my_mask) && ! (mask & (my_mask << 1)))
				calc (x, y+2, mask, next_mask);
		}
	}
}


int main()
{
	cin >> n >> m;

	dp.resize (n+1, vector<long long> (1<<m));
	dp[0][0] = 1;
	for (int x=0; x<n; ++x)
		for (int mask=0; mask<(1<<m); ++mask)
			calc (x, 0, mask, 0);

	cout << dp[n][0];

}
```

## مسائل تمرینی

- [UVA 10359 - Tiling](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1300)
- [UVA 10918 - Tri Tiling](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1859)
- [SPOJ GNY07H (Four Tiling)](https://www.spoj.com/problems/GNY07H/)
- [SPOJ M5TILE (Five Tiling)](https://www.spoj.com/problems/M5TILE/)
- [SPOJ MNTILE (MxN Tiling)](https://www.spoj.com/problems/MNTILE/)
- [SPOJ DOJ1](https://www.spoj.com/problems/DOJ1/)
- [SPOJ DOJ2](https://www.spoj.com/problems/DOJ2/)
- [SPOJ BTCODE_J](https://www.spoj.com/problems/BTCODE_J/)
- [SPOJ PBOARD](https://www.spoj.com/problems/PBOARD/)
- [ACM HDU 4285 - Circuits](http://acm.hdu.edu.cn/showproblem.php?pid=4285)
- [LiveArchive 4608 - Mosaic](https://vjudge.net/problem/UVALive-4608)
- [Timus 1519 - Formula 1](https://acm.timus.ru/problem.aspx?space=1&num=1519)
- [Codeforces Parquet](https://codeforces.com/problemset/problem/26/C)

## مراجع

- [بلاگ توسط EvilBunny](https://web.archive.org/web/20180712171735/https://blog.evilbuggy.com/2018/05/broken-profile-dynamic-programming.html)
- [دستورالعمل TopCoder توسط "syg96"](https://apps.topcoder.com/forums/?module=Thread&start=0&threadID=697369)
- [پست وبلاگ توسط sk765](http://sk765.blogspot.com/2012/02/dynamic-programming-with-profile.html)