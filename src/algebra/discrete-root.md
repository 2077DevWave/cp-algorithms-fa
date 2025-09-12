---
tags:
  - AI Translated
e_maxx_link: discrete-root
---

# ریشه گسسته (Discrete Root)

ببین، مسئله‌ی «ریشه گسسته» خیلی ساده‌ست. فرض کن یه عدد اول $n$ و دو تا عدد صحیح $a$ و $k$ بهت دادن. حالا ازت می‌خوان تمام مقادیر $x$ رو پیدا کنی که توی این معادله صدق کنن:

$x^k \equiv a \pmod n$

## الگوریتم

خب، چطوری حلش کنیم؟ یه راه باحال اینه که مسئله رو به یه مسئله‌ی دیگه که شاید از قبل بلد باشیم، یعنی [مسئله لگاریتم گسسته](discrete-log.md)، تبدیل کنیم.

برای این کار، از یه ابزار قدرتمند به اسم [ریشه اولیه (primitive root)](primitive-root.md) به پیمانه $n$ کمک می‌گیریم. فرض کنیم $g$ یه ریشه اولیه به پیمانه $n$ باشه. یه خبر خوب اینه که چون $n$ اوله، خیالمون راحته که ریشه اولیه حتماً وجود داره و می‌تونیم پیداش کنیم. (زمان پیدا کردنش هم چیزی حدود $O(Ans \cdot \log \phi (n) \cdot \log n) = O(Ans \cdot \log^2 n)$ به علاوه‌ی زمان لازم برای تجزیه $\phi (n)$ هست).

اول یه حالت خاص رو بررسی کنیم و از شرش خلاص شیم: اگه $a = 0$ باشه، جواب خیلی واضحه: فقط $x = 0$ می‌تونه جواب باشه.

حالا بریم سراغ اصل ماجرا. چون می‌دونیم $n$ اوله، هر عددی بین ۱ تا $n-1$ رو میشه به صورت توانی از ریشه اولیه $g$ نوشت. این ویژگی فوق‌العاده‌ست! پس می‌تونیم مسئله‌مون رو اینطوری بازنویسی کنیم:

$(g^y)^k \equiv a \pmod n$

که اینجا در واقع داریم:

$x \equiv g^y \pmod n$

این رابطه رو با یه کم جابجایی می‌تونیم این شکلی هم بنویسیم:

$(g^k)^y \equiv a \pmod n$

الان فقط یه مجهول داریم و اونم $y$ هست. صبر کن ببینم... این که دقیقاً همون مسئله لگاریتم گسسته‌ست! این مسئله رو هم می‌تونیم با الگوریتم معروف «گام کودک-گام غول» (Baby-step giant-step) مال Shanks توی زمان $O(\sqrt {n} \log n)$ حل کنیم (یا بفهمیم که اصلاً جوابی وجود نداره).

بعد از اینکه یه جواب مثل $y_0$ رو پیدا کردیم، یکی از جواب‌های مسئله اصلیمون هم به دست میاد: $x_0 = g^{y_0} \pmod n$.

## پیدا کردن همه‌ی جواب‌ها با داشتن فقط یکی از اون‌ها

خب، ما یه جواب پیدا کردیم. ولی مسئله از ما «تمام» جواب‌ها رو خواسته بود. چطوری بقیه‌شون رو پیدا کنیم؟

یادت باشه که مرتبه‌ی یه ریشه اولیه همیشه $\phi (n)$ هست. یعنی کوچک‌ترین توانی از $g$ که به پیمانه $n$ برابر با ۱ میشه، همین $\phi (n)$ خودمونه. پس اگه مضربی از $\phi (n)$ رو به توان $y_0 \cdot k$ اضافه کنیم، نتیجه نهایی هیچ تغییری نمی‌کنه:

$x^k \equiv g^{ y_0 \cdot k + l \cdot \phi (n)} \equiv a \pmod n \forall l \in Z$

با این حساب، همه‌ی جواب‌های $x$ باید این شکلی باشن:

$x = g^{y_0 + \frac {l \cdot \phi (n)}{k}} \pmod n \forall l \in Z$.

البته اینجا یه شرطی وجود داره: $l$ باید طوری انتخاب بشه که اون کسر، یعنی $\frac {l \cdot \phi (n)}{k}$، یه عدد صحیح از آب دربیاد. این شرط ما رو به فرمول نهایی برای پیدا کردن همه‌ی جواب‌ها می‌رسونه:

$x = g^{y_0 + i \frac {\phi (n)}{\gcd(k, \phi (n))}} \pmod n \forall i \in Z$.

و تمام! این فرمول نهایی برای همه‌ی جواب‌های مسئله ریشه گسسته‌ست.

## پیاده‌سازی

اینم یه پیاده‌سازی کامل که همه‌ی این کارها رو برامون انجام میده: پیدا کردن ریشه اولیه، حل لگاریتم گسسته، و در نهایت پیدا کردن و چاپ همه‌ی جواب‌ها.

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
 
// یه ریشه اولیه به پیمانه p پیدا می‌کنه
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
 
// این برنامه همه‌ی x هایی رو پیدا می‌کنه که توی معادله x^k = a (mod n) صدق می‌کنن
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
 
	// چاپ همه‌ی جواب‌های ممکن
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

## چندتا مسئله برای تمرین

اگه دوست داری دستت رو گرم کنی، این مسئله رو یه نگاهی بنداز:

* [Codeforces - Lunar New Year and a Recursive Sequence](https://codeforces.com/contest/1106/problem/F)