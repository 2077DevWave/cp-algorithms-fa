---
tags:
  - AI Translated
e_maxx_link: bellman_ford
---

# الگوریتم بلمن-فورد

**کوتاه‌ترین مسیر از یک مبدأ با یال‌های دارای وزن منفی**

فرض کنید یک گراف جهت‌دار وزن‌دار $G$ با $n$ رأس و $m$ یال به همراه یک رأس مشخص $v$ به ما داده شده است. می‌خواهیم طول کوتاه‌ترین مسیرها از رأس $v$ به هر رأس دیگر را پیدا کنیم.

برخلاف الگوریتم دایکسترا، این الگوریتم را می‌توان برای گراف‌هایی که یال با وزن منفی دارند نیز به کار برد. اما اگر گراف شامل یک دور منفی باشد، واضح است که کوتاه‌ترین مسیر به برخی از رئوس ممکن است وجود نداشته باشد (زیرا وزن کوتاه‌ترین مسیر باید برابر با منفی بی‌نهایت باشد)؛ با این حال، این الگوریتم می‌تواند طوری تغییر یابد که وجود یک دور با وزن منفی را اعلام کند یا حتی این دور را پیدا کند.

این الگوریتم نام خود را از دو دانشمند آمریکایی، ریچارد بلمن و لستر فورد، گرفته است. فورد در واقع این الگوریتم را در سال ۱۹۵۶ هنگام مطالعه یک مسئله ریاضی دیگر ابداع کرد که در نهایت به زیرمسئله‌ای برای یافتن کوتاه‌ترین مسیرها در گراف کاهش می‌یافت و فورد طرح کلی الگوریتم را برای حل این مسئله ارائه داد. بلمن در سال ۱۹۵۸ مقاله‌ای را به‌طور خاص به مسئله یافتن کوتاه‌ترین مسیر اختصاص داد و در آن مقاله، الگوریتم را به شکلی که امروزه می‌شناسیم، به‌وضوح فرمول‌بندی کرد.

## شرح الگوریتم

فرض می‌کنیم که گراف هیچ دور با وزن منفی ندارد. حالت وجود دور با وزن منفی در بخش جداگانه‌ای در ادامه مورد بحث قرار خواهد گرفت.

یک آرایه از فاصله‌ها به نام $d[0 \ldots n-1]$ ایجاد می‌کنیم که پس از اجرای الگوریتم، پاسخ مسئله را در خود جای خواهد داد. در ابتدا آن را به این صورت پر می‌کنیم: $d[v] = 0$ و سایر عناصر $d[ ]$ برابر با بی‌نهایت $\infty$ هستند.

الگوریتم از چندین فاز تشکیل شده است. هر فاز تمام یال‌های گراف را پیمایش می‌کند و الگوریتم سعی می‌کند عملیات **آرام‌سازی (relaxation)** را در امتداد هر یال $(a,b)$ با وزن $c$ انجام دهد. آرام‌سازی در امتداد یال‌ها تلاشی برای بهبود مقدار $d[b]$ با استفاده از مقدار $d[a] + c$ است. در واقع، این به آن معناست که ما سعی می‌کنیم پاسخ برای این رأس را با استفاده از یال $(a,b)$ و پاسخ فعلی برای رأس $a$ بهبود ببخشیم.

ادعا می‌شود که $n-1$ فاز از الگوریتم برای محاسبه صحیح طول تمام کوتاه‌ترین مسیرها در گراف کافی است (باز هم، فرض بر این است که دورهایی با وزن منفی وجود ندارند). برای رئوس غیرقابل دسترس، فاصله $d[ ]$ برابر با بی‌نهایت $\infty$ باقی خواهد ماند.

## پیاده‌سازی

برخلاف بسیاری از الگوریتم‌های دیگر گراف، برای الگوریتم بلمن-فورد راحت‌تر است که گراف را با استفاده از یک لیست واحد از تمام یال‌ها نمایش دهیم (به جای $n$ لیست از یال‌ها - یال‌های خروجی از هر رأس). پیاده‌سازی را با یک ساختار (`struct`) به نام $\rm Edge$ برای نمایش یال‌ها شروع می‌کنیم. ورودی‌های الگوریتم اعداد $n$ و $m$ ، لیستی از یال‌ها به نام `edges` و رأس شروع $v$ هستند. تمام رئوس از $0$ تا $n-1$ شماره‌گذاری شده‌اند.

### ساده‌ترین پیاده‌سازی

ثابت $\rm INF$ نشان‌دهنده عدد «بی‌نهایت» است — باید به گونه‌ای انتخاب شود که از تمام طول‌های مسیر ممکن بزرگ‌تر باشد.

```cpp
struct Edge {
    int a, b, cost;
};

int n, m, v;
vector<Edge> edges;
const int INF = 1000000000;

void solve()
{
    vector<int> d(n, INF);
    d[v] = 0;
    for (int i = 0; i < n - 1; ++i)
        for (Edge e : edges)
            if (d[e.a] < INF)
                d[e.b] = min(d[e.b], d[e.a] + e.cost);
    // نمایش d، برای مثال، روی صفحه
}
```

بررسی `if (d[e.a] < INF)` فقط در صورتی لازم است که گراف دارای یال با وزن منفی باشد: عدم وجود چنین بررسی‌ای منجر به آرام‌سازی از رئوسی می‌شود که هنوز مسیری به آنها پیدا نشده است و فاصله‌های نادرستی از نوع $\infty - 1$، $\infty - 2$ و غیره ظاهر می‌شوند.

### یک پیاده‌سازی بهتر

این الگوریتم را می‌توان تا حدودی سرعت بخشید: اغلب، ما در چند فاز اول به پاسخ می‌رسیم و در فازهای باقیمانده هیچ کار مفیدی انجام نمی‌شود و فقط با پیمایش تمام یال‌ها زمان تلف می‌شود. بنابراین، یک پرچم (flag) نگه می‌داریم تا مشخص کند آیا در فاز فعلی چیزی تغییر کرده است یا نه، و اگر در فازی هیچ تغییری رخ نداد، الگوریتم می‌تواند متوقف شود. (این بهینه‌سازی رفتار مجانبی را بهبود نمی‌بخشد، یعنی برخی گراف‌ها همچنان به تمام $n-1$ فاز نیاز خواهند داشت، اما به طور قابل توجهی رفتار الگوریتم را «به طور متوسط»، یعنی روی گراف‌های تصادفی، تسریع می‌کند.)

با این بهینه‌سازی، دیگر نیازی به محدود کردن دستی تعداد فازهای الگوریتم به $n-1$ نیست — الگوریتم پس از تعداد فازهای مورد نیاز متوقف خواهد شد.

```cpp
void solve()
{
    vector<int> d(n, INF);
    d[v] = 0;
    for (;;) {
        bool any = false;

        for (Edge e : edges)
            if (d[e.a] < INF)
                if (d[e.b] > d[e.a] + e.cost) {
                    d[e.b] = d[e.a] + e.cost;
                    any = true;
                }

        if (!any)
            break;
    }
    // نمایش d، برای مثال، روی صفحه
}
```

### بازیابی مسیر

حال بیایید ببینیم چگونه الگوریتم را تغییر دهیم تا نه تنها طول کوتاه‌ترین مسیرها را پیدا کند، بلکه امکان بازسازی خود مسیرها را نیز فراهم کند.

برای این کار، یک آرایه دیگر به نام $p[0 \ldots n-1]$ ایجاد می‌کنیم که در آن برای هر رأس، «والد» (predecessor) آن را ذخیره می‌کنیم، یعنی رأسی که قبل از آن در کوتاه‌ترین مسیر قرار دارد. در واقع، کوتاه‌ترین مسیر به هر رأس $a$، یک کوتاه‌ترین مسیر به یک رأس $p[a]$ است که رأس $a$ در انتهای آن اضافه شده است.

توجه داشته باشید که الگوریتم با همان منطق کار می‌کند: فرض می‌کند که کوتاه‌ترین فاصله تا یک رأس قبلاً محاسبه شده و سعی می‌کند کوتاه‌ترین فاصله تا رئوس دیگر را از آن رأس بهبود بخشد. بنابراین، در زمان بهبود، فقط باید $p[ ]$ را به خاطر بسپاریم، یعنی رأسی که این بهبود از آنجا رخ داده است.

در ادامه پیاده‌سازی الگوریتم بلمن-فورد به همراه بازیابی کوتاه‌ترین مسیر به یک گره مشخص $t$ آمده است:

```cpp
void solve()
{
    vector<int> d(n, INF);
    d[v] = 0;
    vector<int> p(n, -1);

    for (;;) {
        bool any = false;
        for (Edge e : edges)
            if (d[e.a] < INF)
                if (d[e.b] > d[e.a] + e.cost) {
                    d[e.b] = d[e.a] + e.cost;
                    p[e.b] = e.a;
                    any = true;
                }
        if (!any)
            break;
    }

    if (d[t] == INF)
        cout << "No path from " << v << " to " << t << ".";
    else {
        vector<int> path;
        for (int cur = t; cur != -1; cur = p[cur])
            path.push_back(cur);
        reverse(path.begin(), path.end());

        cout << "Path from " << v << " to " << t << ": ";
        for (int u : path)
            cout << u << ' ';
    }
}
```

در اینجا از رأس $t$ شروع می‌کنیم، و با دنبال کردن والدها به عقب برمی‌گردیم تا به رأس شروع که والدی ندارد برسیم و تمام رئوس مسیر را در لیست $\rm path$ ذخیره می‌کنیم. این لیست، کوتاه‌ترین مسیر از $v$ به $t$ است، اما به ترتیب معکوس. بنابراین، تابع $\rm reverse()$ را روی $\rm path$ فراخوانی کرده و سپس مسیر را چاپ می‌کنیم.

## اثبات الگوریتم

ابتدا توجه کنید که برای تمام رئوس غیرقابل دسترس $u$، الگوریتم به درستی کار خواهد کرد و برچسب $d[u]$ برابر با بی‌نهایت باقی خواهد ماند (زیرا الگوریتم بلمن-فورد راهی به تمام رئوس قابل دسترس از رأس شروع $v$ پیدا می‌کند و آرام‌سازی برای سایر رئوس باقیمانده هرگز اتفاق نخواهد افتاد).

اکنون گزاره زیر را اثبات می‌کنیم: پس از اجرای فاز $i$-ام، الگوریتم بلمن-فورد به درستی تمام کوتاه‌ترین مسیرهایی را پیدا می‌کند که تعداد یال‌هایشان از $i$ تجاوز نمی‌کند.

به عبارت دیگر، برای هر رأس $a$، فرض کنید $k$ تعداد یال‌های کوتاه‌ترین مسیر به آن باشد (اگر چندین مسیر چنین باشند، می‌توانید هر کدام را در نظر بگیرید). طبق این گزاره، الگوریتم تضمین می‌کند که پس از فاز $k$-ام، کوتاه‌ترین مسیر برای رأس $a$ پیدا خواهد شد.

**اثبات**:
یک رأس دلخواه $a$ را در نظر بگیرید که مسیری از رأس شروع $v$ به آن وجود دارد و یک کوتاه‌ترین مسیر به آن را به صورت $(p_0=v, p_1, \ldots, p_k=a)$ در نظر بگیرید. قبل از فاز اول، کوتاه‌ترین مسیر به رأس $p_0 = v$ به درستی پیدا شده است. در طول فاز اول، یال $(p_0, p_1)$ توسط الگوریتم بررسی شده و بنابراین، فاصله تا رأس $p_1$ پس از فاز اول به درستی محاسبه شده است. با تکرار این استدلال برای $k$ بار، می‌بینیم که پس از فاز $k$-ام، فاصله تا رأس $p_k = a$ به درستی محاسبه می‌شود، که همان چیزی بود که می‌خواستیم ثابت کنیم.

آخرین نکته‌ای که باید توجه داشت این است که هیچ کوتاه‌ترین مسیری نمی‌تواند بیش از $n-1$ یال داشته باشد. بنابراین، الگوریتم تا فاز $(n-1)$-ام ادامه می‌یابد که کافی است. پس از آن، تضمین می‌شود که هیچ آرام‌سازی دیگری فاصله تا هیچ رأسی را بهبود نخواهد داد.

## حالت وجود دور منفی

در تمام بخش‌های بالا فرض کردیم که هیچ دور منفی در گراف وجود ندارد (دقیق‌تر بگوییم، ما به دور منفی‌ای علاقه‌مندیم که از رأس شروع $v$ قابل دسترس باشد، و برای دورهای غیرقابل دسترس، هیچ چیزی در الگوریتم بالا تغییر نمی‌کند). در صورت وجود دور(های) منفی، پیچیدگی‌های بیشتری به وجود می‌آید که ناشی از این واقعیت است که فاصله‌ها تا تمام رئوس موجود در این دور و همچنین فاصله‌ها تا رئوس قابل دسترس از این دور، تعریف نشده‌اند — آنها باید برابر با منفی بی‌نهایت $(- \infty)$ باشند.

به راحتی می‌توان دید که الگوریتم بلمن-فورد می‌تواند به طور بی‌پایان عملیات آرام‌سازی را بین تمام رئوس این دور و رئوس قابل دسترس از آن انجام دهد. بنابراین، اگر تعداد فازها را به $n-1$ محدود نکنید، الگوریتم به طور نامحدود اجرا شده و دائماً فاصله این رئوس را بهبود می‌بخشد.

از این رو، **معیار وجود یک دور با وزن منفی قابل دسترس از رأس مبدأ $v$** را به دست می‌آوریم: پس از فاز $(n-1)$-ام، اگر الگوریتم را برای یک فاز دیگر اجرا کنیم و حداقل یک آرام‌سازی دیگر انجام دهد، آنگاه گراف حاوی یک دور با وزن منفی است که از $v$ قابل دسترس است؛ در غیر این صورت، چنین دوری وجود ندارد.

علاوه بر این، اگر چنین دوری پیدا شود، الگوریتم بلمن-فورد را می‌توان طوری تغییر داد که این دور را به صورت دنباله‌ای از رئوس موجود در آن بازیابی کند. برای این کار، کافی است آخرین رأس $x$ را که در فاز $n$-ام برای آن آرام‌سازی رخ داده است، به خاطر بسپاریم. این رأس یا روی یک دور با وزن منفی قرار دارد یا از آن قابل دسترس است. برای به دست آوردن رئوسی که تضمین شده روی یک دور منفی قرار دارند، از رأس $x$ شروع کرده و $n$ بار به والدها برمی‌گردیم. به این ترتیب، به رأس $y$ می‌رسیم که تضمین شده روی یک دور منفی قرار دارد. سپس باید از این رأس، با دنبال کردن والدها، به عقب برگردیم تا دوباره به همان رأس $y$ برسیم (و این اتفاق خواهد افتاد، زیرا آرام‌سازی در یک دور با وزن منفی به صورت دوری رخ می‌دهد).

### پیاده‌سازی:

```cpp
void solve()
{
    vector<int> d(n, INF);
    d[v] = 0;
    vector<int> p(n, -1);
    int x;
    for (int i = 0; i < n; ++i) {
        x = -1;
        for (Edge e : edges)
            if (d[e.a] < INF)
                if (d[e.b] > d[e.a] + e.cost) {
                    d[e.b] = max(-INF, d[e.a] + e.cost);
                    p[e.b] = e.a;
                    x = e.b;
                }
    }

    if (x == -1)
        cout << "No negative cycle from " << v;
    else {
        int y = x;
        for (int i = 0; i < n; ++i)
            y = p[y];

        vector<int> path;
        for (int cur = y;; cur = p[cur]) {
            path.push_back(cur);
            if (cur == y && path.size() > 1)
                break;
        }
        reverse(path.begin(), path.end());

        cout << "Negative cycle: ";
        for (int u : path)
            cout << u << ' ';
    }
}
```

به دلیل وجود دور منفی، در $n$ تکرار الگوریتم، فاصله‌ها ممکن است به مقادیر بسیار منفی برسند (به اعداد منفی از مرتبه $-n m W$ که $W$ حداکثر قدر مطلق وزن هر یال در گراف است). از این رو در کد، اقدامات احتیاطی بیشتری را برای جلوگیری از سرریز عدد صحیح (integer overflow) به صورت زیر در نظر گرفته‌ایم:

```cpp
d[e.b] = max(-INF, d[e.a] + e.cost);
```

پیاده‌سازی بالا به دنبال یک دور منفی قابل دسترس از یک رأس شروع $v$ می‌گردد؛ با این حال، می‌توان الگوریتم را طوری تغییر داد که به دنبال هر دور منفی در گراف بگردد. برای این کار، باید تمام فاصله‌های $d[i]$ را برابر با صفر قرار دهیم و نه بی‌نهایت — گویی که به دنبال کوتاه‌ترین مسیر از تمام رئوس به طور همزمان هستیم؛ صحت تشخیص دور منفی تحت تأثیر این تغییر قرار نمی‌گیرد.

برای اطلاعات بیشتر در این زمینه — مقاله جداگانه [پیدا کردن دور منفی در گراف](finding-negative-cycle-in-graph.md) را ببینید.

## الگوریتم سریع‌تر کوتاه‌ترین مسیر (SPFA)

SPFA یک بهبود بر الگوریتم بلمن-فورد است که از این واقعیت بهره می‌برد که تمام تلاش‌ها برای آرام‌سازی موفقیت‌آمیز نخواهند بود.
ایده اصلی این است که یک صف (queue) ایجاد کنیم که فقط شامل رئوسی باشد که آرام‌سازی شده‌اند اما هنوز می‌توانند همسایگان خود را بیشتر آرام‌سازی کنند. و هر زمان که بتوانید همسایه‌ای را آرام‌سازی کنید، باید آن را در صف قرار دهید. این الگوریتم نیز مانند بلمن-فورد می‌تواند برای تشخیص دورهای منفی استفاده شود.

بدترین حالت این الگوریتم برابر با $O(n m)$ الگوریتم بلمن-فورد است، اما در عمل بسیار سریع‌تر کار می‌کند و برخی [ادعا می‌کنند که به طور متوسط در زمان $O(m)$ کار می‌کند](https://en.wikipedia.org/wiki/Shortest_Path_Faster_Algorithm#Average-case_performance). با این حال مراقب باشید، زیرا این الگوریتم قطعی (deterministic) است و به راحتی می‌توان مثال‌های نقضی ساخت که الگوریتم را در زمان $O(n m)$ اجرا کنند.

نکات دقیقی در پیاده‌سازی وجود دارد که باید به آنها توجه کرد، مانند این واقعیت که اگر دور منفی وجود داشته باشد، الگوریتم برای همیشه ادامه می‌یابد. برای جلوگیری از این مشکل، می‌توان یک شمارنده ایجاد کرد که تعداد دفعات آرام‌سازی یک رأس را ذخیره کند و به محض اینکه رأسی برای بار $n$-ام آرام‌سازی شد، الگوریتم را متوقف کند. همچنین توجه داشته باشید که دلیلی برای قرار دادن یک رأس در صف وجود ندارد اگر آن رأس از قبل در صف باشد.

```cpp {.cpp file=spfa}
const int INF = 1000000000;
vector<vector<pair<int, int>>> adj;

bool spfa(int s, vector<int>& d) {
    int n = adj.size();
    d.assign(n, INF);
    vector<int> cnt(n, 0);
    vector<bool> inqueue(n, false);
    queue<int> q;

    d[s] = 0;
    q.push(s);
    inqueue[s] = true;
    while (!q.empty()) {
        int v = q.front();
        q.pop();
        inqueue[v] = false;

        for (auto edge : adj[v]) {
            int to = edge.first;
            int len = edge.second;

            if (d[v] + len < d[to]) {
                d[to] = d[v] + len;
                if (!inqueue[to]) {
                    q.push(to);
                    inqueue[to] = true;
                    cnt[to]++;
                    if (cnt[to] > n)
                        return false;  // دور منفی
                }
            }
        }
    }
    return true;
}
```


## مسائل مرتبط در داوری‌های آنلاین

لیستی از مسائلی که با استفاده از الگوریتم بلمن-فورد قابل حل هستند:

* [E-OLYMP #1453 "Ford-Bellman" [سطح: آسان]](https://www.e-olymp.com/en/problems/1453)
* [UVA #423 "MPI Maelstrom" [سطح: آسان]](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=364)
* [UVA #534 "Frogger" [سطح: متوسط]](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=7&page=show_problem&problem=475)
* [UVA #10099 "The Tourist Guide" [سطح: متوسط]](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=12&page=show_problem&problem=1040)
* [UVA #515 "King" [سطح: متوسط]](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=456)
* [UVA 12519 - The Farnsworth Parabox](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3964)

همچنین لیست مسائل در مقاله [پیدا کردن دور منفی در گراف](finding-negative-cycle-in-graph.md) را ببینید.
* [CSES - High Score](https://cses.fi/problemset/task/1673)
* [CSES - Cycle Finding](https://cses.fi/problemset/task/1197)