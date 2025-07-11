---
tags:
  - AI Translated
e_maxx_link: dinic
---

# بیشینه شار - الگوریتم دینیک

الگوریتم دینیک مسئله بیشینه شار را در زمان $O(V^2E)$ حل می‌کند. مسئله بیشینه شار در این مقاله تعریف شده است: [بیشینه شار - الگوریتم‌های فورد-فالکرسون و ادموندز-کارپ](edmonds_karp.md). این الگوریتم توسط یفیم دینیتز در سال ۱۹۷۰ کشف شد.

## تعاریف

یک **شبکه باقیمانده** $G^R$ از شبکه $G$ شبکه‌ای است که برای هر یال $(v, u)\in G$ شامل دو یال زیر است:<br>

-   $(v, u)$ با ظرفیت $c_{vu}^R = c_{vu} - f_{vu}$
-   $(u, v)$ با ظرفیت $c_{uv}^R = f_{vu}$

یک **شار مسدودکننده** در یک شبکه، شاری است که در آن هر مسیر از $s$ به $t$ حداقل یک یال اشباع‌شده توسط این شار داشته باشد. توجه کنید که یک شار مسدودکننده لزوماً بیشینه نیست.

یک **شبکه لایه‌ای** از شبکه $G$ به روش زیر ساخته می‌شود. ابتدا، برای هر رأس $v$ مقدار $level[v]$ را محاسبه می‌کنیم که برابر با طول کوتاه‌ترین مسیر (بدون وزن) از $s$ به این رأس، تنها با استفاده از یال‌هایی با ظرفیت مثبت است. سپس، فقط آن یال‌هایی $(v, u)$ را نگه می‌داریم که شرط $level[v] + 1 = level[u]$ را برآورده کنند. واضح است که این شبکه غیرمدور است.

## الگوریتم

الگوریتم از چندین فاز تشکیل شده است. در هر فاز، شبکه لایه‌ایِ شبکه باقیمانده از $G$ را می‌سازیم. سپس یک شار مسدودکننده دلخواه در شبکه لایه‌ای پیدا کرده و آن را به شار فعلی اضافه می‌کنیم.

## اثبات درستی

نشان می‌دهیم که اگر الگوریتم به پایان برسد، بیشینه شار را پیدا می‌کند.

اگر الگوریتم خاتمه یافته باشد، نتوانسته است یک شار مسدودکننده در شبکه لایه‌ای پیدا کند. این یعنی شبکه لایه‌ای هیچ مسیری از $s$ به $t$ ندارد. این یعنی شبکه باقیمانده هیچ مسیری از $s$ به $t$ ندارد. این یعنی شار، بیشینه است.

## تعداد فازها

الگوریتم در کمتر از $V$ فاز به پایان می‌رسد. برای اثبات این موضوع، ابتدا باید دو لم را اثبات کنیم.

**لم ۱.** فاصله از $s$ به هر رأس پس از هر تکرار کاهش نمی‌یابد، یعنی $level_{i+1}[v] \ge level_i[v]$.

**اثبات.** فاز $i$ و رأس $v$ را ثابت در نظر بگیرید. هر مسیر کوتاهی مانند $P$ از $s$ به $v$ در $G_{i+1}^R$ را در نظر بگیرید. طول $P$ برابر با $level_{i+1}[v]$ است. توجه کنید که $G_{i+1}^R$ فقط می‌تواند شامل یال‌هایی از $G_i^R$ و یال‌های معکوس برای یال‌هایی از $G_i^R$ باشد. اگر $P$ هیچ یال معکوسی برای $G_i^R$ نداشته باشد، آنگاه $level_{i+1}[v] \ge level_i[v]$ زیرا $P$ یک مسیر در $G_i^R$ نیز هست. حال، فرض کنید $P$ حداقل یک یال معکوس دارد. فرض کنید اولین یال از این نوع $(u, w)$ باشد. آنگاه $level_{i+1}[u] \ge level_i[u]$ (به دلیل حالت اول). یال $(u, w)$ به $G_i^R$ تعلق ندارد، پس یال $(w, u)$ تحت تأثیر شار مسدودکننده در تکرار قبلی قرار گرفته است. این یعنی $level_i[u] = level_i[w] + 1$. همچنین، $level_{i+1}[w] = level_{i+1}[u] + 1$. از این دو معادله و اینکه $level_{i+1}[u] \ge level_i[u]$، به دست می‌آوریم $level_{i+1}[w] \ge level_i[w] + 2$. حال می‌توانیم از همین ایده برای باقیمانده مسیر استفاده کنیم.

**لم ۲.** $level_{i+1}[t] > level_i[t]$

**اثبات.** از لم قبلی می‌دانیم $level_{i+1}[t] \ge level_i[t]$. فرض کنید $level_{i+1}[t] = level_i[t]$. توجه کنید که $G_{i+1}^R$ فقط می‌تواند شامل یال‌هایی از $G_i^R$ و یال‌های معکوس برای یال‌هایی از $G_i^R$ باشد. این یعنی یک مسیر کوتاه در $G_i^R$ وجود دارد که توسط شار مسدودکننده، مسدود نشده است. این یک تناقض است.

از این دو لم نتیجه می‌گیریم که کمتر از $V$ فاز وجود دارد، زیرا $level[t]$ افزایش می‌یابد، اما نمی‌تواند بزرگ‌تر از $V - 1$ باشد.

## پیدا کردن شار مسدودکننده

برای پیدا کردن شار مسدودکننده در هر تکرار، می‌توانیم به سادگی با استفاده از DFS از $s$ به $t$ در شبکه لایه‌ای، تا زمانی که امکان دارد، شار ارسال کنیم. برای انجام سریع‌تر این کار، باید یال‌هایی را که دیگر نمی‌توان برای ارسال شار استفاده کرد، حذف کنیم. برای این کار می‌توانیم در هر رأس یک اشاره‌گر نگه داریم که به یال بعدی قابل استفاده اشاره می‌کند.

یک اجرای DFS به تنهایی زمان $O(k+V)$ می‌برد، که در آن $k$ تعداد پیشروی‌های اشاره‌گر در آن اجرا است. در مجموع روی تمام اجراها، تعداد پیشروی‌های اشاره‌گر نمی‌تواند از $E$ تجاوز کند. از طرف دیگر، تعداد کل اجراها از $E$ تجاوز نخواهد کرد، زیرا هر اجرا حداقل یک یال را اشباع می‌کند. به این ترتیب، کل زمان اجرای پیدا کردن یک شار مسدودکننده $O(VE)$ است.

## پیچیدگی

کمتر از $V$ فاز وجود دارد، بنابراین پیچیدگی کل $O(V^2E)$ است.

## شبکه‌های واحد

یک **شبکه واحد** شبکه‌ای است که در آن برای هر رأس به جز $s$ و $t$ **یا یال ورودی یا یال خروجی یکتا بوده و ظرفیت واحد دارد**. این دقیقاً همان موردی است که در شبکه‌ای که برای حل مسئله تطابق بیشینه با استفاده از شار می‌سازیم، وجود دارد.

در شبکه‌های واحد، الگوریتم دینیک در زمان $O(E\sqrt{V})$ کار می‌کند. بیایید این را اثبات کنیم.

اولاً، هر فاز اکنون در زمان $O(E)$ کار می‌کند زیرا هر یال حداکثر یک بار در نظر گرفته می‌شود.

ثانیاً، فرض کنید که تاکنون $\sqrt{V}$ فاز اجرا شده است. در این صورت تمام مسیرهای افزایشی با طول $\le\sqrt{V}$ پیدا شده‌اند. فرض کنید $f$ شار فعلی و $f'$ بیشینه شار باشد. تفاضل آن‌ها $f' - f$ را در نظر بگیرید. این یک شار در $G^R$ با مقدار $|f'| - |f|$ است و روی هر یال یا $0$ است یا $1$. این شار را می‌توان به $|f'| - |f|$ مسیر از $s$ به $t$ و احتمالاً چند دور تجزیه کرد. از آنجایی که شبکه واحد است، این مسیرها نمی‌توانند رأس مشترک داشته باشند، بنابراین تعداد کل رأس‌ها $\ge (|f'| - |f|)\sqrt{V}$ است، اما این مقدار همچنین $\le V$ است. بنابراین، در $\sqrt{V}$ تکرار دیگر، قطعاً بیشینه شار را پیدا خواهیم کرد.

### شبکه‌های با ظرفیت واحد

در یک حالت کلی‌تر که تمام یال‌ها ظرفیت واحد دارند، *اما تعداد یال‌های ورودی و خروجی نامحدود است*، مسیرها نمی‌توانند یال مشترک داشته باشند (به جای رأس مشترک). به روشی مشابه، می‌توان کران $\sqrt E$ را برای تعداد تکرارها اثبات کرد، بنابراین زمان اجرای الگوریتم دینیک در چنین شبکه‌هایی حداکثر $O(E \sqrt E)$ است.

در نهایت، همچنین می‌توان اثبات کرد که تعداد فازها در شبکه‌های با ظرفیت واحد از $O(V^{2/3})$ تجاوز نمی‌کند، که یک تخمین جایگزین $O(EV^{2/3})$ برای شبکه‌هایی با تعداد یال‌های بسیار زیاد ارائه می‌دهد.

## پیاده‌سازی

```cpp
struct FlowEdge {
    int v, u;
    long long cap, flow = 0;
    FlowEdge(int v, int u, long long cap) : v(v), u(u), cap(cap) {}
};

struct Dinic {
    const long long flow_inf = 1e18;
    vector<FlowEdge> edges;
    vector<vector<int>> adj;
    int n, m = 0;
    int s, t;
    vector<int> level, ptr;
    queue<int> q;

    Dinic(int n, int s, int t) : n(n), s(s), t(t) {
        adj.resize(n);
        level.resize(n);
        ptr.resize(n);
    }

    void add_edge(int v, int u, long long cap) {
        edges.emplace_back(v, u, cap);
        edges.emplace_back(u, v, 0);
        adj[v].push_back(m);
        adj[u].push_back(m + 1);
        m += 2;
    }

    bool bfs() {
        while (!q.empty()) {
            int v = q.front();
            q.pop();
            for (int id : adj[v]) {
                if (edges[id].cap == edges[id].flow)
                    continue;
                if (level[edges[id].u] != -1)
                    continue;
                level[edges[id].u] = level[v] + 1;
                q.push(edges[id].u);
            }
        }
        return level[t] != -1;
    }

    long long dfs(int v, long long pushed) {
        if (pushed == 0)
            return 0;
        if (v == t)
            return pushed;
        for (int& cid = ptr[v]; cid < (int)adj[v].size(); cid++) {
            int id = adj[v][cid];
            int u = edges[id].u;
            if (level[v] + 1 != level[u])
                continue;
            long long tr = dfs(u, min(pushed, edges[id].cap - edges[id].flow));
            if (tr == 0)
                continue;
            edges[id].flow += tr;
            edges[id ^ 1].flow -= tr;
            return tr;
        }
        return 0;
    }

    long long flow() {
        long long f = 0;
        while (true) {
            fill(level.begin(), level.end(), -1);
            level[s] = 0;
            q.push(s);
            if (!bfs())
                break;
            fill(ptr.begin(), ptr.end(), 0);
            while (long long pushed = dfs(s, flow_inf)) {
                f += pushed;
            }
        }
        return f;
    }
};
```

## مسائل تمرینی

*   [SPOJ: FASTFLOW](https://www.spoj.com/problems/FASTFLOW/)