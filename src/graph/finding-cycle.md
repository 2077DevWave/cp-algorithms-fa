---
tags:
  - AI Translated
e_maxx_link: finding-cycle
---

# بررسی غیرمدور بودن یک گراف و پیدا کردن یک دور در $O(M)$

یک گراف جهت‌دار یا بدون جهت را بدون طوقه و یال چندگانه در نظر بگیرید. می‌خواهیم بررسی کنیم که آیا این گراف غیرمدور است یا خیر، و اگر نبود، یک دور در آن پیدا کنیم.

می‌توانیم این مسئله را با استفاده از [جستجوی اول عمق (DFS)](depth-first-search.md) در زمان $O(M)$ حل کنیم، که در آن $M$ تعداد یال‌ها است.

## الگوریتم

یک سری DFS در گراف اجرا می‌کنیم. در ابتدا تمام رئوس سفید (0) رنگ می‌شوند. از هر رأس ملاقات‌نشده (سفید)، DFS را شروع می‌کنیم، هنگام ورود به آن، آن را خاکستری (1) و هنگام خروج، آن را سیاه (2) علامت می‌زنیم. اگر DFS به یک رأس خاکستری حرکت کند، آنگاه یک دور پیدا کرده‌ایم (اگر گراف بدون جهت باشد، یال منتهی به رأس والد در نظر گرفته نمی‌شود). خودِ دور را می‌توان با استفاده از آرایه `parent` بازسازی کرد.

## پیاده‌سازی

این یک پیاده‌سازی برای گراف جهت‌دار است.

```cpp
int n;
vector<vector<int>> adj;
vector<char> color;
vector<int> parent;
int cycle_start, cycle_end;

bool dfs(int v) {
    color[v] = 1;
    for (int u : adj[v]) {
        if (color[u] == 0) {
            parent[u] = v;
            if (dfs(u))
                return true;
        } else if (color[u] == 1) {
            cycle_end = v;
            cycle_start = u;
            return true;
        }
    }
    color[v] = 2;
    return false;
}

void find_cycle() {
    color.assign(n, 0);
    parent.assign(n, -1);
    cycle_start = -1;

    for (int v = 0; v < n; v++) {
        if (color[v] == 0 && dfs(v))
            break;
    }

    if (cycle_start == -1) {
        cout << "Acyclic" << endl;
    } else {
        vector<int> cycle;
        cycle.push_back(cycle_start);
        for (int v = cycle_end; v != cycle_start; v = parent[v])
            cycle.push_back(v);
        cycle.push_back(cycle_start);
        reverse(cycle.begin(), cycle.end());

        cout << "Cycle found: ";
        for (int v : cycle)
            cout << v << " ";
        cout << endl;
    }
}
```

این یک پیاده‌سازی برای گراف بدون جهت است.
توجه داشته باشید که در نسخه بدون جهت، اگر رأسی مانند `v` سیاه رنگ شود، دیگر هرگز توسط DFS ملاقات نخواهد شد.
دلیل این امر این است که ما قبلاً هنگام اولین ملاقات با `v`، تمام یال‌های متصل به آن را پیمایش کرده‌ایم.
مؤلفه همبندی شامل `v` (پس از حذف یال بین `v` و والدش) باید یک درخت باشد، اگر DFS پردازش `v` را بدون یافتن دور به پایان رسانده باشد.
بنابراین، حتی نیازی به تمایز بین حالت‌های خاکستری و سیاه نداریم.
در نتیجه می‌توانیم وکتور `char` به نام `color` را به یک وکتور `boolean` به نام `visited` تبدیل کنیم.

```cpp
int n;
vector<vector<int>> adj;
vector<bool> visited;
vector<int> parent;
int cycle_start, cycle_end;

bool dfs(int v, int par) { // ارسال رأس و رأس والد آن
    visited[v] = true;
    for (int u : adj[v]) {
        if(u == par) continue; // صرف‌نظر کردن از یال منتهی به رأس والد
        if (visited[u]) {
            cycle_end = v;
            cycle_start = u;
            return true;
        }
        parent[u] = v;
        if (dfs(u, parent[u]))
            return true;
    }
    return false;
}

void find_cycle() {
    visited.assign(n, false);
    parent.assign(n, -1);
    cycle_start = -1;

    for (int v = 0; v < n; v++) {
        if (!visited[v] && dfs(v, parent[v]))
            break;
    }

    if (cycle_start == -1) {
        cout << "Acyclic" << endl;
    } else {
        vector<int> cycle;
        cycle.push_back(cycle_start);
        for (int v = cycle_end; v != cycle_start; v = parent[v])
            cycle.push_back(v);
        cycle.push_back(cycle_start);

        cout << "Cycle found: ";
        for (int v : cycle)
            cout << v << " ";
        cout << endl;
    }
}
```
### مسائل تمرینی:

- [AtCoder : Reachability in Functional Graph](https://atcoder.jp/contests/abc357/tasks/abc357_e)
- [CSES : Round Trip](https://cses.fi/problemset/task/1669)
- [CSES : Round Trip II](https://cses.fi/problemset/task/1678/)