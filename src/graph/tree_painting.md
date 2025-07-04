---
tags:
  - AI Translated
e_maxx_link: tree_painting
---

# رنگ‌آمیزی یال‌های درخت

این یک مسئله‌ی نسبتاً رایج است. درختی با $N$ رأس به نام $G$ داده شده است. دو نوع پرس‌وجو (query) وجود دارد: نوع اول رنگ کردن یک یال است و نوع دوم پرس‌وجو در مورد تعداد یال‌های رنگ‌شده بین دو رأس است.

در اینجا یک راه‌حل نسبتاً ساده (با استفاده از [segment tree](../data_structures/segment_tree.md)) توصیف می‌کنیم که هر پرس‌وجو را در زمان $O(\log N)$ پاسخ می‌دهد.
مرحله پیش‌پردازش $O(N)$ زمان خواهد برد.

## الگوریتم

ابتدا، باید [LCA](lca.md) را پیدا کنیم تا هر پرس‌وجوی نوع دوم $(i,j)$ را به دو پرس‌وجوی $(l,i)$ و $(l,j)$ کاهش دهیم، که در آن $l$ همان LCA رئوس $i$ و $j$ است.
پاسخ پرس‌وجوی $(i,j)$ برابر با مجموع این دو زیرپرس‌وجو خواهد بود.
هر دوی این پرس‌وجوها ساختار خاصی دارند: رأس اول جدّ رأس دوم است.
در ادامه‌ی این مقاله، فقط در مورد این نوع خاص از پرس‌وجوها صحبت خواهیم کرد.

با توصیف مرحله **پیش‌پردازش** شروع می‌کنیم.
یک `depth-first search` از ریشه درخت اجرا کرده و `Euler tour` این `depth-first search` را ثبت می‌کنیم (هر رأس هنگام اولین بازدید و هر بار که از یکی از فرزندانش برمی‌گردیم به لیست اضافه می‌شود).
از همین تکنیک می‌توان در پیش‌پردازش LCA نیز استفاده کرد.

این لیست شامل هر یال خواهد بود (به این معنی که اگر $i$ و $j$ دو سر یک یال باشند، در جایی از لیست، $i$ و $j$ همسایه خواهند بود)، و هر یال دقیقاً دو بار ظاهر می‌شود: یک بار در جهت رو به جلو (از $i$ به $j$، جایی که رأس $i$ به ریشه نزدیک‌تر از رأس $j$ است) و یک بار در جهت مخالف (از $j$ به $i$).

برای این یال‌ها دو لیست می‌سازیم.
لیست اول رنگ تمام یال‌ها در جهت رو به جلو را ذخیره می‌کند و لیست دوم رنگ تمام یال‌ها در جهت مخالف را.
اگر یال رنگ شده باشد از $1$ و در غیر این صورت از $0$ استفاده می‌کنیم.
روی هر یک از این دو لیست یک `segment tree` (برای جمع با یک تغییر) می‌سازیم و آن‌ها را $T1$ و $T2$ می‌نامیم.

حال به پرس‌وجویی به شکل $(i,j)$ پاسخ می‌دهیم، که در آن $i$ جدّ $j$ است.
باید مشخص کنیم که چند یال در مسیر بین $i$ و $j$ رنگ شده‌اند.
مکان اولین ظهور $i$ و $j$ را در `Euler tour` پیدا می‌کنیم و آن‌ها را $p$ و $q$ می‌نامیم (اگر این موقعیت‌ها را از قبل در مرحله پیش‌پردازش محاسبه کنیم، این کار در $O(1)$ قابل انجام است).
در این صورت، **پاسخ** به پرس‌وجو برابر است با مجموع $T1[p..q-1]$ منهای مجموع $T2[p..q-1]$.

**چرا؟**
بازه $[p,q]$ را در `Euler tour` در نظر بگیرید.
این بازه شامل تمام یال‌های مسیر مورد نیاز ما از $i$ به $j$ است، اما مجموعه‌ای از یال‌ها را نیز در بر می‌گیرد که روی مسیرهای دیگری از $i$ قرار دارند.
با این حال، یک تفاوت بزرگ بین یال‌های مورد نیاز ما و بقیه یال‌ها وجود دارد: یال‌های مورد نیاز ما فقط یک بار و در جهت رو به جلو لیست می‌شوند، در حالی که سایر یال‌ها دو بار ظاهر می‌شوند: یک بار در جهت رو به جلو و یک بار در جهت مخالف.
بنابراین، تفاضل $T1[p..q-1] - T2[p..q-1]$ پاسخ صحیح را به ما می‌دهد (استفاده از بازه تا `q-1` ضروری است، زیرا در غیر این صورت، یال اضافی خروجی از رأس $j$ نیز به حساب می‌آید).
پرس‌وجوی جمع در `segment tree` در $O(\log N)$ اجرا می‌شود.

پاسخ به **پرس‌وجوی نوع اول** (رنگ‌آمیزی یک یال) حتی ساده‌تر است - فقط باید $T1$ و $T2$ را به‌روزرسانی کنیم، یعنی یک به‌روزرسانی تکی روی عنصری که متناظر با یال ماست انجام دهیم (پیدا کردن یال در لیست، باز هم، اگر این جستجو را در حین پیش‌پردازش انجام دهید، در $O(1)$ امکان‌پذیر است).
یک تغییر تکی در `segment tree` در $O(\log N)$ انجام می‌شود.

## پیاده‌سازی

در اینجا پیاده‌سازی کامل راه‌حل، شامل محاسبه LCA، آمده است:

```cpp
const int INF = 1000 * 1000 * 1000;

typedef vector<vector<int>> graph;

vector<int> dfs_list;
vector<int> edges_list;
vector<int> h;

void dfs(int v, const graph& g, const graph& edge_ids, int cur_h = 1) {
    h[v] = cur_h;
    dfs_list.push_back(v);
    for (size_t i = 0; i < g[v].size(); ++i) {
        if (h[g[v][i]] == -1) {
            edges_list.push_back(edge_ids[v][i]);
            dfs(g[v][i], g, edge_ids, cur_h + 1);
            edges_list.push_back(edge_ids[v][i]);
            dfs_list.push_back(v);
        }
    }
}

vector<int> lca_tree;
vector<int> first;

void lca_tree_build(int i, int l, int r) {
    if (l == r) {
        lca_tree[i] = dfs_list[l];
    } else {
        int m = (l + r) >> 1;
        lca_tree_build(i + i, l, m);
        lca_tree_build(i + i + 1, m + 1, r);
        int lt = lca_tree[i + i], rt = lca_tree[i + i + 1];
        lca_tree[i] = h[lt] < h[rt] ? lt : rt;
    }
}

void lca_prepare(int n) {
    lca_tree.assign(dfs_list.size() * 8, -1);
    lca_tree_build(1, 0, (int)dfs_list.size() - 1);

    first.assign(n, -1);
    for (int i = 0; i < (int)dfs_list.size(); ++i) {
        int v = dfs_list[i];
        if (first[v] == -1)
            first[v] = i;
    }
}

int lca_tree_query(int i, int tl, int tr, int l, int r) {
    if (tl == l && tr == r)
        return lca_tree[i];
    int m = (tl + tr) >> 1;
    if (r <= m)
        return lca_tree_query(i + i, tl, m, l, r);
    if (l > m)
        return lca_tree_query(i + i + 1, m + 1, tr, l, r);
    int lt = lca_tree_query(i + i, tl, m, l, m);
    int rt = lca_tree_query(i + i + 1, m + 1, tr, m + 1, r);
    return h[lt] < h[rt] ? lt : rt;
}

int lca(int a, int b) {
    if (first[a] > first[b])
        swap(a, b);
    return lca_tree_query(1, 0, (int)dfs_list.size() - 1, first[a], first[b]);
}

vector<int> first1, first2;
vector<char> edge_used;
vector<int> tree1, tree2;

void query_prepare(int n) {
    first1.resize(n - 1, -1);
    first2.resize(n - 1, -1);
    for (int i = 0; i < (int)edges_list.size(); ++i) {
        int j = edges_list[i];
        if (first1[j] == -1)
            first1[j] = i;
        else
            first2[j] = i;
    }

    edge_used.resize(n - 1);
    tree1.resize(edges_list.size() * 8);
    tree2.resize(edges_list.size() * 8);
}

void sum_tree_update(vector<int>& tree, int i, int l, int r, int j, int delta) {
    tree[i] += delta;
    if (l < r) {
        int m = (l + r) >> 1;
        if (j <= m)
            sum_tree_update(tree, i + i, l, m, j, delta);
        else
            sum_tree_update(tree, i + i + 1, m + 1, r, j, delta);
    }
}

int sum_tree_query(const vector<int>& tree, int i, int tl, int tr, int l, int r) {
    if (l > r || tl > tr)
        return 0;
    if (tl == l && tr == r)
        return tree[i];
    int m = (tl + tr) >> 1;
    if (r <= m)
        return sum_tree_query(tree, i + i, tl, m, l, r);
    if (l > m)
        return sum_tree_query(tree, i + i + 1, m + 1, tr, l, r);
    return sum_tree_query(tree, i + i, tl, m, l, m) +
           sum_tree_query(tree, i + i + 1, m + 1, tr, m + 1, r);
}

int query(int v1, int v2) {
    return sum_tree_query(tree1, 1, 0, (int)edges_list.size() - 1, first[v1], first[v2] - 1) -
           sum_tree_query(tree2, 1, 0, (int)edges_list.size() - 1, first[v1], first[v2] - 1);
}

int main() {
    // خواندن گراف
    int n;
    scanf("%d", &n);
    graph g(n), edge_ids(n);
    for (int i = 0; i < n - 1; ++i) {
        int v1, v2;
        scanf("%d%d", &v1, &v2);
        --v1, --v2;
        g[v1].push_back(v2);
        g[v2].push_back(v1);
        edge_ids[v1].push_back(i);
        edge_ids[v2].push_back(i);
    }

    h.assign(n, -1);
    dfs(0, g, edge_ids);
    lca_prepare(n);
    query_prepare(n);

    for (;;) {
        if () {
            // درخواست برای رنگ‌آمیزی یال x؛
            // اگر start برابر با true باشد، یال رنگ می‌شود، در غیر این صورت رنگ‌آمیزی
            // حذف می‌شود
            edge_used[x] = start;
            sum_tree_update(tree1, 1, 0, (int)edges_list.size() - 1, first1[x],
                            start ? 1 : -1);
            sum_tree_update(tree2, 1, 0, (int)edges_list.size() - 1, first2[x],
                            start ? 1 : -1);
        } else {
            // پرس‌وجو برای تعداد یال‌های رنگ‌شده در مسیر بین v1 و v2
            int l = lca(v1, v2);
            int result = query(l, v1) + query(l, v2);
            // result - پاسخ درخواست
        }
    }
}
```