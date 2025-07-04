---
tags:
  - AI Translated
e_maxx_link: bridge-searching
---

# یافتن پل‌ها در یک گراف با پیچیدگی $O(N+M)$

یک گراف غیرجهت‌دار به ما داده شده است. پل به یالی گفته می‌شود که با حذف آن، گراف ناهمبند می‌شود (یا به عبارت دقیق‌تر، تعداد مؤلفه‌های همبندی در گراف افزایش می‌یابد). هدف، پیدا کردن تمام پل‌ها در گراف داده شده است.

به‌طور غیررسمی، مسئله به این صورت بیان می‌شود: با داشتن نقشه‌ای از شهرها که با جاده‌ها به هم متصل شده‌اند، تمام جاده‌های «مهم» را پیدا کنید، یعنی جاده‌هایی که با حذف آن‌ها، مسیر بین یک جفت شهر از بین می‌رود.

الگوریتم توصیف شده در اینجا بر اساس [جستجوی اول عمق](depth-first-search.md) است و پیچیدگی زمانی $O(N+M)$ دارد، که در آن $N$ تعداد رأس‌ها و $M$ تعداد یال‌ها در گراف است.

توجه داشته باشید که مقاله‌ای با عنوان [یافتن پل‌ها به صورت آنلاین](bridge-searching-online.md) نیز وجود دارد - برخلاف الگوریتم آفلاین که در اینجا شرح داده شده، الگوریتم آنلاین قادر است لیست تمام پل‌ها را در یک گراف در حال تغییر (با فرض اینکه تنها نوع تغییر، اضافه شدن یال‌های جدید باشد) نگهداری کند.

## الگوریتم

یک رأس دلخواه از گراف مانند $root$ را انتخاب کرده و [جستجوی اول عمق](depth-first-search.md) را از آن اجرا کنید. به نکته زیر توجه کنید (که اثبات آن آسان است):

-   فرض کنید در حال اجرای DFS هستیم و یال‌های خروجی از رأس $v$ را بررسی می‌کنیم. یال فعلی $(v, to)$ یک پل است اگر و تنها اگر هیچ‌کدام از رأس $to$ و نوادگانش در درخت پیمایش DFS، یک یال برگشتی به رأس $v$ یا هر یک از اجدادش نداشته باشند. در واقع، این شرط به این معنی است که هیچ راه دیگری از $v$ به `to` به جز یال $(v, to)$ وجود ندارد.

اکنون باید یاد بگیریم که چگونه این شرط را برای هر رأس به طور کارآمد بررسی کنیم. ما از «زمان ورود به گره» که توسط جستجوی اول عمق محاسبه می‌شود، استفاده خواهیم کرد.

پس، فرض کنید $\mathtt{tin}[v]$ زمان ورود به گره $v$ را نشان دهد. ما آرایه‌ای به نام $\mathtt{low}$ معرفی می‌کنیم که به ما امکان می‌دهد گره‌ای با زودترین زمان ورود که در جستجوی DFS یافت می‌شود و گره $v$ می‌تواند با یک یال از خودش یا نوادگانش به آن برسد را ذخیره کنیم. $\mathtt{low}[v]$ برابر با کمینه مقدار $\mathtt{tin}[v]$، زمان‌های ورود $\mathtt{tin}[p]$ برای هر گره $p$ که از طریق یک یال برگشتی $(v, p)$ به گره $v$ متصل است و مقادیر $\mathtt{low}[to]$ برای هر رأس `to` که فرزند مستقیم $v$ در درخت DFS است، می‌باشد:

$$\mathtt{low}[v] = \min \left\{ 
    \begin{array}{l}
    \mathtt{tin}[v] \\ 
    \mathtt{tin}[p]  &\text{ for all }p\text{ for which }(v, p)\text{ is a back edge} \\ 
    \mathtt{low}[to] &\text{ for all }to\text{ for which }(v, to)\text{ is a tree edge}
    \end{array}
\right\}$$

حال، یک یال برگشتی از رأس $v$ یا یکی از نوادگان آن به یکی از اجدادش وجود دارد اگر و تنها اگر رأس $v$ فرزندی مانند `to` داشته باشد که $\mathtt{low}[to] \leq \mathtt{tin}[v]$ باشد. اگر $\mathtt{low}[to] = \mathtt{tin}[v]$ باشد، یال برگشتی مستقیماً به $v$ می‌رسد، در غیر این صورت به یکی از اجداد $v$ می‌رسد.

بنابراین، یال فعلی $(v, to)$ در درخت DFS یک پل است اگر و تنها اگر $\mathtt{low}[to] > \mathtt{tin}[v]$ باشد.

## پیاده‌سازی

در پیاده‌سازی باید سه حالت را از هم تشخیص داد: زمانی که در درخت DFS روی یک یال به سمت پایین می‌رویم، زمانی که یک یال برگشتی به یکی از اجداد رأس پیدا می‌کنیم و زمانی که به والد یک رأس برمی‌گردیم. این حالت‌ها عبارتند از:

-   $\mathtt{visited}[to] = false$ - یال بخشی از درخت DFS است؛
-   $\mathtt{visited}[to] = true$ && $to \neq parent$ - یال، یک یال برگشتی به یکی از اجداد است؛
-   $to = parent$ - یال به والد در درخت DFS برمی‌گردد.

برای پیاده‌سازی این، به یک تابع جستجوی اول عمق نیاز داریم که رأس والدِ گره فعلی را به عنوان ورودی بپذیرد.

در موارد یال‌های چندگانه، هنگام نادیده گرفتن یالِ از سمت والد، باید مراقب باشیم. برای حل این مشکل، می‌توانیم یک فلگ `parent_skipped` اضافه کنیم که تضمین می‌کند والد را فقط یک بار نادیده می‌گیریم.

```cpp
void IS_BRIDGE(int v,int to); // some function to process the found bridge
int n; // number of nodes
vector<vector<int>> adj; // adjacency list of graph

vector<bool> visited;
vector<int> tin, low;
int timer;
 
void dfs(int v, int p = -1) {
    visited[v] = true;
    tin[v] = low[v] = timer++;
    bool parent_skipped = false;
    for (int to : adj[v]) {
        if (to == p && !parent_skipped) {
            parent_skipped = true;
            continue;
        }
        if (visited[to]) {
            low[v] = min(low[v], tin[to]);
        } else {
            dfs(to, v);
            low[v] = min(low[v], low[to]);
            if (low[to] > tin[v])
                IS_BRIDGE(v, to);
        }
    }
}
 
void find_bridges() {
    timer = 0;
    visited.assign(n, false);
    tin.assign(n, -1);
    low.assign(n, -1);
    for (int i = 0; i < n; ++i) {
        if (!visited[i])
            dfs(i);
    }
}
```

تابع اصلی `find_bridges` است؛ این تابع مقداردهی‌های اولیه لازم را انجام می‌دهد و جستجوی اول عمق را در هر مؤلفه همبندی گراف شروع می‌کند.

تابع `IS_BRIDGE(a, b)` تابعی است که این واقعیت را که یال $(a, b)$ یک پل است، پردازش می‌کند، برای مثال، آن را چاپ می‌کند.

توجه داشته باشید که این پیاده‌سازی در صورتی که گراف دارای یال‌های چندگانه باشد، به درستی کار نمی‌کند، زیرا آن‌ها را نادیده می‌گیرد. البته، یال‌های چندگانه هرگز بخشی از جواب نخواهند بود، بنابراین `IS_BRIDGE` می‌تواند به طور اضافی بررسی کند که پل گزارش‌شده یک یال چندگانه نیست. به عنوان راه حل جایگزین، می‌توان به جای رأس والد، اندیس یالی که برای ورود به رأس استفاده شده را به تابع `dfs` پاس داد (و اندیس تمام رأس‌ها را ذخیره کرد).

## مسائل تمرینی

-   [UVA #796 "Critical Links"](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=737) [سختی: کم]
-   [UVA #610 "Street Directions"](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=551) [سختی: متوسط]
-   [Case of the Computer Network (Codeforces Round #310 Div. 1 E)](http://codeforces.com/problemset/problem/555/E) [سختی: سخت]
-   [UVA 12363 - Hedge Mazes](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=3785)
-   [UVA 315 - Network](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=251)
-   [GYM - Computer Network (J)](http://codeforces.com/gym/100114)
-   [SPOJ - King Graffs Defense](http://www.spoj.com/problems/GRAFFDEF/)
-   [SPOJ - Critical Edges](http://www.spoj.com/problems/EC_P/)
-   [Codeforces - Break Up](http://codeforces.com/contest/700/problem/C)
-   [Codeforces - Tourist Reform](http://codeforces.com/contest/732/problem/F)
-   [Codeforces - Non-academic problem](https://codeforces.com/contest/1986/problem/F)