---
tags:
  - AI Translated
e_maxx_link: discrete-root
---

# ریشه گسسته

مسئله‌ی یافتن ریشه گسسته به صورت زیر تعریف می‌شود. با داشتن یک عدد اول $n$ و دو عدد صحیح $a$ و $k$، تمام مقادیر $x$ را بیابید که در شرط زیر صدق کنند:

$x^k \equiv a \pmod n$

## الگوریتم

این مسئله را با کاهش آن به [مسئله لگاریتم گسسته](discrete-log.md) حل خواهیم کرد.

بیایید از مفهوم [ریشه اولیه](primitive-root.md) به پیمانه $n$ استفاده کنیم. فرض کنید $g$ یک ریشه اولیه به پیمانه $n$ باشد. توجه داشته باشید که چون $n$ اول است، ریشه اولیه حتماً وجود دارد و می‌توان آن را در زمان $O(Ans \cdot \log \phi (n) \cdot \log n) = O(Ans \cdot \log^2 n)$ به علاوه زمان لازم برای تجزیه $\phi (n)$ پیدا کرد.

می‌توانیم به راحتی حالتی که $a = 0$ است را نادیده بگیریم. در این حالت، واضح است که تنها یک جواب وجود دارد: $x = 0$.

از آنجایی که می‌دانیم $n$ اول است و هر عدد بین ۱ و $n-1$ را می‌توان به صورت توانی از ریشه اولیه نمایش داد، می‌توانیم مسئله ریشه گسسته را به صورت زیر بازنویسی کنیم:

$(g^y)^k \equiv a \pmod n$

که در آن

$x \equiv g^y \pmod n$

این رابطه را نیز می‌توان به صورت زیر نوشت:

$(g^k)^y \equiv a \pmod n$

اکنون یک مجهول $y$ داریم که این خود یک مسئله لگاریتم گسسته است. جواب را می‌توان با استفاده از الگوریتم گام کودک-گام غول Shanks در زمان $O(\sqrt {n} \log n)$ پیدا کرد (یا می‌توانیم بررسی کنیم که آیا جوابی وجود ندارد).

با پیدا کردن یک جواب $y_0$، یکی از جواب‌های مسئله ریشه گسسته برابر با $x_0 = g^{y_0} \pmod n$ خواهد بود.

## یافتن تمام جواب‌ها با داشتن یک جواب

برای حل کامل مسئله، باید با داشتن یکی از جواب‌ها، یعنی $x_0 = g^{y_0} \pmod n$، تمام جواب‌های دیگر را پیدا کنیم.

این واقعیت را به یاد بیاوریم که یک ریشه اولیه همیشه دارای مرتبه $\phi (n)$ است، یعنی کوچک‌ترین توانی از $g$ که برابر با ۱ می‌شود، $\phi (n)$ است. بنابراین، اگر مضربی از $\phi (n)$ را به توان $y_0 \cdot k$ اضافه کنیم، همچنان همان مقدار را به دست می‌آوریم:

$x^k \equiv g^{ y_0 \cdot k + l \cdot \phi (n)} \equiv a \pmod n \forall l \in Z$

از این رو، تمام جواب‌ها به شکل زیر هستند:

$x = g^{y_0 + \frac {l \cdot \phi (n)}{k}} \pmod n \forall l \in Z$.

که در آن $l$ به گونه‌ای انتخاب می‌شود که کسر باید یک عدد صحیح باشد. این شرط منجر به فرمول نهایی زیر برای تمام جواب‌ها می‌شود:

$x = g^{y_0 + i \frac {\phi (n)}{\gcd(k, \phi (n))}} \pmod n \forall i \in Z$.

این فرمول نهایی برای تمام جواب‌های مسئله ریشه گسسته است.

## پیاده‌سازی

در ادامه یک پیاده‌سازی کامل آمده است که شامل رویه‌هایی برای یافتن ریشه اولیه، لگاریتم گسسته، و یافتن و چاپ تمام جواب‌ها است.

```cpp
int gcd(int a, int b) {
	return a ? gcd(b % a, a) : b;
}
 
int powmod(int a, int b, int p) {
	int res = 1;
	while (b > 0) {
		if (b & 1) {
			res = res * a % p;
		}
		a = a * a % p;
		b >>= 1;
	}
	return res;
}
 
// ریشه اولیه به پیمانه p را پیدا می‌کند
int generator(int p) {
	vector<int> fact;
	int phi = p-1, n = phi;
	for (int i = 2; i * i <= n; ++i) {
		if (n % i == 0) {
			fact.push_back(i);
			while (n % i == 0)
				n /= i;
		}
	}
	if (n > 1)
		fact.push_back(n);
 
	for (int res = 2; res <= p; ++res) {
		bool ok = true;
		for (int factor : fact) {
			if (powmod(res, phi / factor, p) == 1) {
				ok = false;
				break;
			}
		}
		if (ok) return res;
	}
	return -1;
}
 
// این برنامه تمام اعداد x را پیدا می‌کند که در رابطه x^k = a (mod n) صدق می‌کنند
int main() {
	int n, k, a;
	scanf("%d %d %d", &n, &k, &a);
	if (a == 0) {
		puts("1\n0");
		return 0;
	}
 
	int g = generator(n);
 
	// الگوریتم لگاریتم گسسته گام کودک-گام غول
	int sq = (int) sqrt (n + .0) + 1;
	vector<pair<int, int>> dec(sq);
	for (int i = 1; i <= sq; ++i)
		dec[i-1] = {powmod(g, i * sq * k % (n - 1), n), i};
	sort(dec.begin(), dec.end());
	int any_ans = -1;
	for (int i = 0; i < sq; ++i) {
		int my = powmod(g, i * k % (n - 1), n) * a % n;
		auto it = lower_bound(dec.begin(), dec.end(), make_pair(my, 0));
		if (it != dec.end() && it->first == my) {
			any_ans = it->second * sq - i;
			break;
		}
	}
	if (any_ans == -1) {
		puts("0");
		return 0;
	}
 
	// چاپ تمام جواب‌های ممکن
	int delta = (n-1) / gcd(k, n-1);
	vector<int> ans;
	for (int cur = any_ans % delta; cur < n-1; cur += delta)
		ans.push_back(powmod(g, cur, n));
	sort(ans.begin(), ans.end());
	printf("%d\n", ans.size());
	for (int answer : ans)
		printf("%d ", answer);
}
```

## مسائل تمرینی

* [Codeforces - Lunar New Year and a Recursive Sequence](https://codeforces.com/contest/1106/problem/F)