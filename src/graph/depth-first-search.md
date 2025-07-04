---
tags:
  - AI Translated
e_maxx_link: depth-first-search
---

# جستجوی اول عمق

جستجوی اول عمق یکی از الگوریتم‌های اصلی گراف است.

جستجوی اول عمق اولین مسیر از نظر ترتیب الفبایی (lexicographical) را در گراف از یک رأس مبدأ $u$ به هر رأس دیگر پیدا می‌کند.
جستجوی اول عمق کوتاه‌ترین مسیرها را در یک درخت نیز پیدا می‌کند (چون تنها یک مسیر ساده وجود دارد)، اما در گراف‌های کلی اینطور نیست.

این الگوریتم در زمان $O(m + n)$ اجرا می‌شود که در آن $n$ تعداد رأس‌ها و $m$ تعداد یال‌ها است.

## شرح الگوریتم

ایده اصلی DFS این است که تا حد امکان به عمق گراف برویم و هنگامی که به رأسی رسیدیم که هیچ رأس مجاور ملاقات‌نشده‌ای ندارد، بازگشت به عقب (backtrack) انجام دهیم.

توصیف / پیاده‌سازی این الگوریتم به صورت بازگشتی بسیار آسان است:
ما جستجو را از یک رأس شروع می‌کنیم.
پس از ملاقات یک رأس، برای هر رأس مجاور آن که قبلاً ملاقات نکرده‌ایم، یک DFS دیگر اجرا می‌کنیم.
به این ترتیب تمام رأس‌هایی که از رأس شروع قابل‌دسترسی هستند را ملاقات می‌کنیم.

برای جزئیات بیشتر، پیاده‌سازی را بررسی کنید.

## کاربردهای جستجوی اول عمق

  * پیدا کردن یک مسیر دلخواه در گراف از رأس مبدأ $u$ به تمام رأس‌ها.
  
  * پیدا کردن اولین مسیر از نظر ترتیب الفبایی در گراف از مبدأ $u$ به تمام رأس‌ها.
  
  * بررسی اینکه آیا یک رأس در یک درخت، جد (ancestor) یک رأس دیگر است یا خیر:
  
    در ابتدا و انتهای هر فراخوانی جستجو، «زمان» ورود و خروج هر رأس را به خاطر می‌سپاریم.
    اکنون می‌توانید پاسخ را برای هر جفت رأس $(i, j)$ در زمان $O(1)$ پیدا کنید:
    رأس $i$ جد رأس $j$ است اگر و تنها اگر $\text{entry}[i] < \text{entry}[j]$ و $\text{exit}[i] > \text{exit}[j]$ باشد.
  
  * پیدا کردن پایین‌ترین جد مشترک (LCA) دو رأس.
  
  * مرتب‌سازی توپولوژیکی:
  
    یک سری جستجوی اول عمق را اجرا کنید تا هر رأس دقیقاً یک بار در زمان $O(n + m)$ ملاقات شود.
    ترتیب توپولوژیکی مورد نیاز، رأس‌هایی خواهد بود که بر اساس زمان خروج به صورت نزولی مرتب شده‌اند.
  
  
  * بررسی اینکه آیا یک گراف داده‌شده بدون دور است و پیدا کردن دورها در یک گراف. (همانطور که در بالا ذکر شد، با شمردن یال‌های برگشتی (back edges) در هر مؤلفه همبند).
  
  * پیدا کردن مؤلفه‌های قویاً همبند در یک گراف جهت‌دار:
  
    ابتدا یک مرتب‌سازی توپولوژیکی روی گراف انجام دهید.
    سپس گراف را ترانهاده (transpose) کرده و یک سری دیگر جستجوی اول عمق را به ترتیبی که توسط مرتب‌سازی توپولوژیکی مشخص شده است، اجرا کنید. برای هر فراخوانی DFS، مؤلفه‌ای که توسط آن ایجاد می‌شود یک مؤلفه قویاً همبند است.
  
  * پیدا کردن پل‌ها در یک گراف بدون‌جهت:
  
    ابتدا با اجرای یک سری جستجوی اول عمق، گراف داده‌شده را به یک گراف جهت‌دار تبدیل کنید و هر یال را در حین پیمایش، در جهتی که طی کرده‌ایم، جهت‌دار کنید. دوم، مؤلفه‌های قویاً همبند را در این گراف جهت‌دار پیدا کنید. پل‌ها یال‌هایی هستند که دو سر آن‌ها به مؤلفه‌های قویاً همبند متفاوتی تعلق دارند.

## دسته‌بندی یال‌های یک گراف

ما می‌توانیم یال‌های یک گراف، $G$، را با استفاده از زمان ورود و خروج گره‌های انتهایی $u$ و $v$ یال‌های $(u,v)$ دسته‌بندی کنیم.
این دسته‌بندی‌ها اغلب برای مسائلی مانند [پیدا کردن پل‌ها](bridge-searching.md) و [پیدا کردن نقاط برش](cutpoints.md) استفاده می‌شوند.

ما یک DFS اجرا می‌کنیم و یال‌های مشاهده‌شده را با استفاده از قوانین زیر دسته‌بندی می‌کنیم:

اگر $v$ ملاقات نشده باشد:

* یال درختی (Tree Edge) - اگر $v$ بعد از $u$ ملاقات شود، آنگاه یال $(u,v)$ یک یال درختی نامیده می‌شود. به عبارت دیگر، اگر $v$ برای اولین بار ملاقات شود و $u$ در حال حاضر در حال ملاقات باشد، آنگاه $(u,v)$ یال درختی نامیده می‌شود. این یال‌ها یک درخت DFS را تشکیل می‌دهند و از این رو یال‌های درختی نام گرفته‌اند.

اگر $v$ قبل از $u$ ملاقات شده باشد:

* یال‌های برگشتی (Back edges) - اگر $v$ جد $u$ باشد، آنگاه یال $(u,v)$ یک یال برگشتی است. $v$ دقیقاً زمانی جد است که ما قبلاً وارد $v$ شده‌ایم، اما هنوز از آن خارج نشده‌ایم. یال‌های برگشتی یک دور را کامل می‌کنند، زیرا یک مسیر از جد $v$ به نسل $u$ (در بازگشت DFS) و یک یال از نسل $u$ به جد $v$ (یال برگشتی) وجود دارد، بنابراین یک دور تشکیل می‌شود. دورها را می‌توان با استفاده از یال‌های برگشتی تشخیص داد.

* یال‌های پیشرو (Forward Edges) - اگر $v$ نسل (descendant) $u$ باشد، آنگاه یال $(u, v)$ یک یال پیشرو است. به عبارت دیگر، اگر ما قبلاً $v$ را ملاقات کرده و از آن خارج شده باشیم و $\text{entry}[u] < \text{entry}[v]$ باشد، آنگاه یال $(u,v)$ یک یال پیشرو تشکیل می‌دهد.
* یال‌های متقاطع (Cross Edges): اگر $v$ نه جد و نه نسل $u$ باشد، آنگاه یال $(u, v)$ یک یال متقاطع است. به عبارت دیگر، اگر ما قبلاً $v$ را ملاقات کرده و از آن خارج شده باشیم و $\text{entry}[u] > \text{entry}[v]$ باشد، آنگاه $(u,v)$ یک یال متقاطع است.

**قضیه**. فرض کنید $G$ یک گراف بدون‌جهت باشد. در این صورت، اجرای DFS روی $G$ هر یال مشاهده‌شده را به عنوان یال درختی یا یال برگشتی دسته‌بندی می‌کند، یعنی یال‌های پیشرو و متقاطع فقط در گراف‌های جهت‌دار وجود دارند.

فرض کنید $(u,v)$ یک یال دلخواه از $G$ باشد و بدون از دست دادن کلیت، $u$ قبل از $v$ ملاقات شود، یعنی $\text{entry}[u] < \text{entry}[v]$. از آنجایی که DFS هر یال را فقط یک بار پردازش می‌کند، تنها دو راه برای پردازش یال $(u,v)$ و در نتیجه دسته‌بندی آن وجود دارد:

* اولین باری که یال $(u,v)$ را پیمایش می‌کنیم در جهت $u$ به $v$ است. از آنجایی که $\text{entry}[u] < \text{entry}[v]$، ماهیت بازگشتی DFS به این معنی است که گره $v$ به طور کامل پیمایش و در نتیجه از آن خارج خواهد شد، قبل از اینکه بتوانیم «در پشته فراخوانی به بالا برگردیم» تا از گره $u$ خارج شویم. بنابراین، گره $v$ باید در اولین باری که DFS یال $(u,v)$ را از $u$ به $v$ پیمایش می‌کند، ملاقات‌نشده باشد، زیرا در غیر این صورت، جستجو یال $(u,v)$ را از $v$ به $u$ قبل از خروج از گره $v$ پیمایش می‌کرد، چون گره‌های $u$ و $v$ همسایه هستند. بنابراین، یال $(u,v)$ یک یال درختی است.

* اولین باری که یال $(u,v)$ را پیمایش می‌کنیم در جهت $v$ به $u$ است. از آنجا که گره $u$ را قبل از گره $v$ کشف کرده‌ایم و یال‌ها را فقط یک بار پردازش می‌کنیم، تنها راهی که می‌توانیم یال $(u,v)$ را در جهت $v$ به $u$ پیمایش کنیم این است که مسیر دیگری از $u$ به $v$ وجود داشته باشد که شامل یال $(u,v)$ نباشد، و در نتیجه $u$ را به جد $v$ تبدیل کند. بنابراین، یال $(u,v)$ یک دور را کامل می‌کند، زیرا از نسل، $v$، به جد، $u$ می‌رود که هنوز از آن خارج نشده‌ایم. بنابراین، یال $(u,v)$ یک یال برگشتی است.

از آنجایی که تنها دو راه برای پردازش یال $(u,v)$ وجود دارد، با دو حالت و دسته‌بندی‌های حاصل از آنها که در بالا ذکر شد، اجرای DFS روی $G$ هر یال مشاهده‌شده را به عنوان یال درختی یا یال برگشتی دسته‌بندی می‌کند، یعنی یال‌های پیشرو و متقاطع فقط در گراف‌های جهت‌دار وجود دارند. این اثبات را کامل می‌کند.

## پیاده‌سازی

```cpp
vector<vector<int>> adj; // گراف به صورت لیست مجاورت
int n; // تعداد رأس‌ها

vector<bool> visited;

void dfs(int v) {
	visited[v] = true;
	for (int u : adj[v]) {
		if (!visited[u])
			dfs(u);
    }
}
```
این ساده‌ترین پیاده‌سازی جستجوی اول عمق است.
همانطور که در بخش کاربردها توضیح داده شد، ممکن است محاسبه زمان ورود و خروج و رنگ رأس نیز مفید باشد.
ما تمام رأس‌ها را با رنگ ۰ رنگ‌آمیزی می‌کنیم اگر آنها را ملاقات نکرده باشیم، با رنگ ۱ اگر آنها را ملاقات کرده باشیم، و با رنگ ۲ اگر قبلاً از رأس خارج شده باشیم.

در اینجا یک پیاده‌سازی عمومی‌تر آورده شده است که این موارد را نیز محاسبه می‌کند:

```cpp
vector<vector<int>> adj; // گراف به صورت لیست مجاورت
int n; // تعداد رأس‌ها

vector<int> color;

vector<int> time_in, time_out;
int dfs_timer = 0;

void dfs(int v) {
	time_in[v] = dfs_timer++;
	color[v] = 1;
	for (int u : adj[v])
		if (color[u] == 0)
			dfs(u);
	color[v] = 2;
	time_out[v] = dfs_timer++;
}
```

## مسائل تمرینی

* [SPOJ: ABCPATH](http://www.spoj.com/problems/ABCPATH/)
* [SPOJ: EAGLE1](http://www.spoj.com/problems/EAGLE1/)
* [Codeforces: Kefa and Park](http://codeforces.com/problemset/problem/580/C)
* [Timus:Werewolf](http://acm.timus.ru/problem.aspx?space=1&num=1242)
* [Timus:Penguin Avia](http://acm.timus.ru/problem.aspx?space=1&num=1709)
* [Timus:Two Teams](http://acm.timus.ru/problem.aspx?space=1&num=1106)
* [SPOJ - Ada and Island](http://www.spoj.com/problems/ADASEA/)
* [UVA 657 - The die is cast](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=598)
* [SPOJ - Sheep](http://www.spoj.com/problems/KOZE/)
* [SPOJ - Path of the Rightenous Man](http://www.spoj.com/problems/RIOI_2_3/)
* [SPOJ - Validate the Maze](http://www.spoj.com/problems/MAKEMAZE/)
* [SPOJ - Ghosts having Fun](http://www.spoj.com/problems/GHOSTS/)
* [Codeforces - Underground Lab](http://codeforces.com/contest/781/problem/C)
* [DevSkill - Maze Tester (archived)](http://web.archive.org/web/20200319103915/https://www.devskill.com/CodingProblems/ViewProblem/3)
* [DevSkill - Tourist (archived)](http://web.archive.org/web/20190426175135/https://devskill.com/CodingProblems/ViewProblem/17)
* [Codeforces - Anton and Tree](http://codeforces.com/contest/734/problem/E)
* [Codeforces - Transformation: From A to B](http://codeforces.com/contest/727/problem/A)
* [Codeforces - One Way Reform](http://codeforces.com/contest/723/problem/E)
* [Codeforces - Centroids](http://codeforces.com/contest/709/problem/E)
* [Codeforces - Generate a String](http://codeforces.com/contest/710/problem/E)
* [Codeforces - Broken Tree](http://codeforces.com/contest/758/problem/E)
* [Codeforces - Dasha and Puzzle](http://codeforces.com/contest/761/problem/E)
* [Codeforces - Making genome In Berland](http://codeforces.com/contest/638/problem/B)
* [Codeforces - Road Improvement](http://codeforces.com/contest/638/problem/C)
* [Codeforces - Garland](http://codeforces.com/contest/767/problem/C)
* [Codeforces - Labeling Cities](http://codeforces.com/contest/794/problem/D)
* [Codeforces - Send the Fool Futher!](http://codeforces.com/contest/802/problem/K)
* [Codeforces - The tag Game](http://codeforces.com/contest/813/problem/C)
* [Codeforces - Leha and Another game about graphs](http://codeforces.com/contest/841/problem/D)
* [Codeforces - Shortest path problem](http://codeforces.com/contest/845/problem/G)
* [Codeforces - Upgrading Tree](http://codeforces.com/contest/844/problem/E)
* [Codeforces - From Y to Y](http://codeforces.com/contest/849/problem/C)
* [Codeforces - Chemistry in Berland](http://codeforces.com/contest/846/problem/E)
* [Codeforces - Wizards Tour](http://codeforces.com/contest/861/problem/F)
* [Codeforces - Ring Road](http://codeforces.com/contest/24/problem/A)
* [Codeforces - Mail Stamps](http://codeforces.com/contest/29/problem/C)
* [Codeforces - Ant on the Tree](http://codeforces.com/contest/29/problem/D)
* [SPOJ - Cactus](http://www.spoj.com/problems/CAC/)
* [SPOJ - Mixing Chemicals](http://www.spoj.com/problems/AMR10J/)