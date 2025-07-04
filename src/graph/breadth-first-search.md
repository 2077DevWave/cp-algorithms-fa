---
tags:
  - AI Translated
e_maxx_link: breadth-first-search
---

# جستجوی اول سطح

جستجوی اول سطح (BFS) یکی از الگوریتم‌های پایه‌ای و ضروری برای جستجو در گراف‌ها است.

به دلیل نحوه‌ی عملکرد این الگوریتم، مسیری که جستجوی اول سطح به هر رأس پیدا می‌کند، کوتاه‌ترین مسیر به آن رأس است؛ یعنی مسیری که در گراف‌های بدون وزن، کمترین تعداد یال را داشته باشد.

این الگوریتم در زمان $O(n + m)$ اجرا می‌شود که در آن $n$ تعداد رئوس و $m$ تعداد یال‌ها است.

## توصیف الگوریتم

این الگوریتم یک گراف بدون وزن و شناسه‌ی رأس مبدأ $s$ را به عنوان ورودی می‌گیرد. گراف ورودی می‌تواند جهت‌دار یا بدون جهت باشد، این موضوع برای الگوریتم تفاوتی ندارد.

می‌توان این الگوریتم را مانند آتشی در حال گسترش در گراف درک کرد: در گام صفرم، فقط رأس مبدأ $s$ آتش گرفته است. در هر گام، آتشی که در هر رأس شعله‌ور است، به تمام همسایگانش سرایت می‌کند. در یک تکرار الگوریتم، «حلقه‌ی آتش» به اندازه‌ی یک واحد در عرض گسترش می‌یابد (و نام الگوریتم نیز از همین‌جا گرفته شده است).

دقیق‌تر بگوییم، الگوریتم را می‌توان به این صورت بیان کرد: یک صف $q$ ایجاد کنید که رئوس مورد پردازش را در خود نگه دارد و یک آرایه بولین $used[]$ که برای هر رأس مشخص می‌کند آیا آتش گرفته (یا بازدید شده) است یا نه.

در ابتدا، رأس مبدأ $s$ را به صف اضافه کنید و $used[s] = true$ قرار دهید و برای سایر رئوس $v$ مقدار $used[v] = false$ را تنظیم کنید. سپس، تا زمانی که صف خالی شود، در یک حلقه تکرار کنید و در هر تکرار، یک رأس را از ابتدای صف بردارید. تمام یال‌های خروجی از این رأس را پیمایش کنید و اگر برخی از این یال‌ها به رئوسی می‌روند که هنوز آتش نگرفته‌اند، آن‌ها را آتش بزنید و در صف قرار دهید.

در نتیجه، وقتی صف خالی می‌شود، «حلقه‌ی آتش» شامل تمام رئوس قابل دسترس از مبدأ $s$ است و هر رأس به کوتاه‌ترین شکل ممکن پیدا شده است. همچنین می‌توانید طول کوتاه‌ترین مسیرها را محاسبه کنید (که فقط نیاز به نگهداری آرایه‌ای از طول مسیرها $d[]$ دارد) و همچنین اطلاعات لازم برای بازیابی تمام این کوتاه‌ترین مسیرها را ذخیره کنید (برای این کار، لازم است آرایه‌ای از «والدها» $p[]$ نگهداری شود که برای هر رأس، رأسی را که از طریق آن به این رأس رسیده‌ایم، ذخیره می‌کند).

## پیاده‌سازی

کد الگوریتم توصیف‌شده را به زبان‌های C++ و Java می‌نویسیم.

=== "C++"
    ```cpp
    vector<vector<int>> adj;  // نمایش به صورت لیست مجاورت
    int n; // تعداد رئوس
    int s; // رأس مبدأ

    queue<int> q;
    vector<bool> used(n);
    vector<int> d(n), p(n);

    q.push(s);
    used[s] = true;
    p[s] = -1;
    while (!q.empty()) {
        int v = q.front();
        q.pop();
        for (int u : adj[v]) {
            if (!used[u]) {
                used[u] = true;
                q.push(u);
                d[u] = d[v] + 1;
                p[u] = v;
            }
        }
    }
    ```
=== "Java"
    ```java
    ArrayList<ArrayList<Integer>> adj = new ArrayList<>(); // نمایش به صورت لیست مجاورت
        
    int n; // تعداد رئوس
    int s; // رأس مبدأ


    LinkedList<Integer> q = new LinkedList<Integer>();
    boolean used[] = new boolean[n];
    int d[] = new int[n];
    int p[] = new int[n];

    q.push(s);
    used[s] = true;
    p[s] = -1;
    while (!q.isEmpty()) {
        int v = q.pop();
        for (int u : adj.get(v)) {
            if (!used[u]) {
                used[u] = true;
                q.push(u);
                d[u] = d[v] + 1;
                p[u] = v;
            }
        }
    }
    ```
    
اگر بخواهیم کوتاه‌ترین مسیر از مبدأ به رأس $u$ را بازیابی و نمایش دهیم، می‌توان این کار را به روش زیر انجام داد:
    
=== "C++"
    ```cpp
    if (!used[u]) {
        cout << "No path!";
    } else {
        vector<int> path;
        for (int v = u; v != -1; v = p[v])
            path.push_back(v);
        reverse(path.begin(), path.end());
        cout << "Path: ";
        for (int v : path)
            cout << v << " ";
    }
    ```
=== "Java"
    ```java
    if (!used[u]) {
        System.out.println("No path!");
    } else {
        ArrayList<Integer> path = new ArrayList<Integer>();
        for (int v = u; v != -1; v = p[v])
            path.add(v);
        Collections.reverse(path);
        for(int v : path)
            System.out.println(v);
    }
    ```
    
## کاربردهای BFS

*   پیدا کردن کوتاه‌ترین مسیر از یک مبدأ به سایر رئوس در یک گراف بدون وزن.

*   پیدا کردن تمام مؤلفه‌های همبندی در یک گراف بدون جهت در زمان $O(n + m)$:
برای این کار، کافی است BFS را از هر رأس شروع کنیم، به جز رئوسی که قبلاً در اجراهای قبلی بازدید شده‌اند.
بنابراین، ما BFS معمولی را از هر رأس اجرا می‌کنیم، اما آرایه $used[]$ را هر بار که یک مؤلفه همبندی جدید پیدا می‌کنیم، بازنشانی نمی‌کنیم و زمان اجرای کل همچنان $O(n + m)$ خواهد بود (اجرای چندین BFS بر روی گراف بدون صفر کردن آرایه $used []$ را یک سری جستجوی اول سطح می‌نامند).

*   پیدا کردن راه‌حل یک مسئله یا بازی با کمترین تعداد حرکت، اگر هر حالت از بازی بتواند با یک رأس از گراف نمایش داده شود و انتقال از یک حالت به حالت دیگر یال‌های گراف باشند.

*   پیدا کردن کوتاه‌ترین مسیر در گرافی با وزن‌های ۰ یا ۱:
این کار تنها به یک تغییر کوچک در جستجوی اول سطح معمولی نیاز دارد: به جای نگهداری آرایه $used[]$، اکنون بررسی می‌کنیم که آیا فاصله تا رأس کوتاه‌تر از فاصله فعلی پیدا شده است یا خیر. سپس اگر یال فعلی وزن صفر داشته باشد، آن را به ابتدای صف اضافه می‌کنیم، در غیر این صورت آن را به انتهای صف اضافه می‌کنیم. این تغییر با جزئیات بیشتر در مقاله [BFS 0-1](01_bfs.md) توضیح داده شده است.

*   پیدا کردن کوتاه‌ترین دور در یک گراف جهت‌دار بدون وزن:
یک جستجوی اول سطح را از هر رأس شروع کنید.
به محض اینکه سعی کنیم از رأس فعلی به رأس مبدأ بازگردیم، کوتاه‌ترین دور شامل رأس مبدأ را پیدا کرده‌ایم.
در این نقطه می‌توانیم BFS را متوقف کرده و یک BFS جدید از رأس بعدی شروع کنیم.
از بین تمام این دورها (حداکثر یک دور از هر BFS)، کوتاه‌ترین را انتخاب کنید.

*   پیدا کردن تمام یال‌هایی که روی هر کوتاه‌ترین مسیر بین یک زوج رأس داده شده $(a, b)$ قرار دارند.
برای این کار، دو جستجوی اول سطح اجرا کنید:
یکی از $a$ و دیگری از $b$.
فرض کنید $d_a []$ آرایه حاوی کوتاه‌ترین فاصله‌های به دست آمده از BFS اول (از $a$) و $d_b []$ آرایه حاوی کوتاه‌ترین فاصله‌های به دست آمده از BFS دوم از $b$ باشد.
حال برای هر یال $(u, v)$ به راحتی می‌توان بررسی کرد که آیا آن یال روی هر کوتاه‌ترین مسیر بین $a$ و $b$ قرار دارد یا خیر:
معیار، شرط $d_a [u] + 1 + d_b [v] = d_a [b]$ است.

*   پیدا کردن تمام رئوسی که روی هر کوتاه‌ترین مسیر بین یک زوج رأس داده شده $(a, b)$ قرار دارند.
برای انجام این کار، دو جستجوی اول سطح اجرا کنید:
یکی از $a$ و دیگری از $b$.
فرض کنید $d_a []$ آرایه حاوی کوتاه‌ترین فاصله‌های به دست آمده از BFS اول (از $a$) و $d_b []$ آرایه حاوی کوتاه‌ترین فاصله‌های به دست آمده از BFS دوم (از $b$) باشد.
حال برای هر رأس به راحتی می‌توان بررسی کرد که آیا روی هر کوتاه‌ترین مسیر بین $a$ و $b$ قرار دارد یا خیر:
معیار، شرط $d_a [v] + d_b [v] = d_a [b]$ است.

*   پیدا کردن کوتاه‌ترین گشت با طول زوج از یک رأس مبدأ $s$ به یک رأس مقصد $t$ در یک گراف بدون وزن:
برای این کار، باید یک گراف کمکی بسازیم که رئوس آن نمایانگر حالت $(v, c)$ هستند؛ در این حالت، $v$ گره فعلی و $c$ (با مقدار ۰ یا ۱) زوجیت طول گشت تا آن گره است.
هر یال $(u, v)$ از گراف اصلی در این گراف جدید به دو یال $((u, 0), (v, 1))$ و $((u, 1), (v, 0))$ تبدیل می‌شود.
پس از آن، یک BFS اجرا می‌کنیم تا کوتاه‌ترین گشت از رأس شروع $(s, 0)$ به رأس پایانی $(t, 0)$ را پیدا کنیم.<br>**نکته**: این مورد به دلیلی از اصطلاح «گشت» (walk) به جای «مسیر» (path) استفاده می‌کند، زیرا رئوس ممکن است در گشت پیدا شده تکرار شوند تا طول آن زوج شود. مسئله پیدا کردن کوتاه‌ترین _مسیر_ با طول زوج در گراف‌های جهت‌دار NP-Complete است و در گراف‌های بدون جهت [در زمان خطی قابل حل است](https://onlinelibrary.wiley.com/doi/abs/10.1002/net.3230140403)، اما با رویکردی بسیار پیچیده‌تر.

## مسائل تمرینی

* [SPOJ: AKBAR](http://spoj.com/problems/AKBAR)
* [SPOJ: NAKANJ](http://www.spoj.com/problems/NAKANJ/)
* [SPOJ: WATER](http://www.spoj.com/problems/WATER)
* [SPOJ: MICE AND MAZE](http://www.spoj.com/problems/MICEMAZE/)
* [Timus: Caravans](http://acm.timus.ru/problem.aspx?space=1&num=2034)
* [DevSkill - Holloween Party (archived)](http://web.archive.org/web/20200930162803/http://www.devskill.com/CodingProblems/ViewProblem/60)
* [DevSkill - Ohani And The Link Cut Tree (archived)](http://web.archive.org/web/20170216192002/http://devskill.com:80/CodingProblems/ViewProblem/150)
* [SPOJ - Spiky Mazes](http://www.spoj.com/problems/SPIKES/)
* [SPOJ - Four Chips (hard)](http://www.spoj.com/problems/ADV04F1/)
* [SPOJ - Inversion Sort](http://www.spoj.com/problems/INVESORT/)
* [Codeforces - Shortest Path](http://codeforces.com/contest/59/problem/E)
* [SPOJ - Yet Another Multiple Problem](http://www.spoj.com/problems/MULTII/)
* [UVA 11392 - Binary 3xType Multiple](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2387)
* [UVA 10968 - KuPellaKeS](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1909)
* [Codeforces - Police Stations](http://codeforces.com/contest/796/problem/D)
* [Codeforces - Okabe and City](http://codeforces.com/contest/821/problem/D)
* [SPOJ - Find the Treasure](http://www.spoj.com/problems/DIGOKEYS/)
* [Codeforces - Bear and Forgotten Tree 2](http://codeforces.com/contest/653/problem/E)
* [Codeforces - Cycle in Maze](http://codeforces.com/contest/769/problem/C)
* [UVA - 11312 - Flipping Frustration](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2287)
* [SPOJ - Ada and Cycle](http://www.spoj.com/problems/ADACYCLE/)
* [CSES - Labyrinth](https://cses.fi/problemset/task/1193)
* [CSES - Message Route](https://cses.fi/problemset/task/1667/)
* [CSES - Monsters](https://cses.fi/problemset/task/1194)