---
tags:
  - AI Translated
e_maxx_link: deleting_in_log_n
---

# حذف از یک داده ساختار در زمان $O(T(n)\log n)$

فرض کنید یک ساختار داده دارید که امکان اضافه کردن عناصر را در زمان $O(T(n))$ فراهم می‌کند.

این مقاله تکنیکی را توصیف می‌کند که امکان حذف را به صورت offline در زمان $O(T(n)\log n)$ فراهم می‌کند.

## الگوریتم

هر عنصر برای بازه‌های زمانی مشخصی بین عملیات اضافه کردن و حذف، در data structure زنده است.

یک segment tree روی کوئری‌ها می‌سازیم.

هر بازه‌ای که در آن یک عنصر زنده است، به $O(\log n)$ گره از درخت تقسیم می‌شود.

هر کوئری که در آن وضعیت ساختار را جویا می‌شویم، در برگ متناظر آن قرار می‌دهیم.

حال برای پردازش تمام کوئری‌ها، یک DFS روی segment tree اجرا می‌کنیم.

هنگام ورود به یک گره، تمام عناصری را که در آن گره قرار دارند اضافه می‌کنیم.

سپس به سراغ فرزندان آن گره می‌رویم یا (اگر گره برگ باشد) به کوئری‌ها پاسخ می‌دهیم.

هنگام خروج از گره، باید عملیات اضافه کردن را لغو (undo) کنیم.

توجه داشته باشید که اگر ساختار را در زمان $O(T(n))$ تغییر دهیم، می‌توانیم با نگهداری یک stack از تغییرات، این تغییرات را در زمان $O(T(n))$ به حالت قبل بازگردانیم (roll back).

توجه داشته باشید که عملیات بازگردانی (rollback) پیچیدگی زمانی سرشکن (amortized complexity) را نامعتبر می‌کند.

## نکات

ایده ساختن segment tree روی بازه‌های زمانی که یک موجودیت در آن فعال است، می‌تواند برای مسائلی غیر از مسائل data structure نیز به کار رود.

برای نمونه، مسائل زیر را ببینید.

## پیاده‌سازی

این پیاده‌سازی برای مسئله‌ی [همبندی پویا (dynamic connectivity)](https://en.wikipedia.org/wiki/Dynamic_connectivity) است.

این پیاده‌سازی می‌تواند یال اضافه کند، یال حذف کند و تعداد مؤلفه‌های همبند را بشمارد.

```{.cpp
struct dsu_save {
    int v, rnkv, u, rnku;

    dsu_save() {}

    dsu_save(int _v, int _rnkv, int _u, int _rnku)
        : v(_v), rnkv(_rnkv), u(_u), rnku(_rnku) {}
};

struct dsu_with_rollbacks {
    vector<int> p, rnk;
    int comps;
    stack<dsu_save> op;

    dsu_with_rollbacks() {}

    dsu_with_rollbacks(int n) {
        p.resize(n);
        rnk.resize(n);
        for (int i = 0; i < n; i++) {
            p[i] = i;
            rnk[i] = 0;
        }
        comps = n;
    }

    int find_set(int v) {
        return (v == p[v]) ? v : find_set(p[v]);
    }

    bool unite(int v, int u) {
        v = find_set(v);
        u = find_set(u);
        if (v == u)
            return false;
        comps--;
        if (rnk[v] > rnk[u])
            swap(v, u);
        op.push(dsu_save(v, rnk[v], u, rnk[u]));
        p[v] = u;
        if (rnk[u] == rnk[v])
            rnk[u]++;
        return true;
    }

    void rollback() {
        if (op.empty())
            return;
        dsu_save x = op.top();
        op.pop();
        comps++;
        p[x.v] = x.v;
        rnk[x.v] = x.rnkv;
        p[x.u] = x.u;
        rnk[x.u] = x.rnku;
    }
};

struct query {
    int v, u;
    bool united;
    query(int _v, int _u) : v(_v), u(_u) {
    }
};

struct QueryTree {
    vector<vector<query>> t;
    dsu_with_rollbacks dsu;
    int T;

    QueryTree() {}

    QueryTree(int _T, int n) : T(_T) {
        dsu = dsu_with_rollbacks(n);
        t.resize(4 * T + 4);
    }

    void add_to_tree(int v, int l, int r, int ul, int ur, query& q) {
        if (ul > ur)
            return;
        if (l == ul && r == ur) {
            t[v].push_back(q);
            return;
        }
        int mid = (l + r) / 2;
        add_to_tree(2 * v, l, mid, ul, min(ur, mid), q);
        add_to_tree(2 * v + 1, mid + 1, r, max(ul, mid + 1), ur, q);
    }

    void add_query(query q, int l, int r) {
        add_to_tree(1, 0, T - 1, l, r, q);
    }

    void dfs(int v, int l, int r, vector<int>& ans) {
        for (query& q : t[v]) {
            q.united = dsu.unite(q.v, q.u);
        }
        if (l == r)
            ans[l] = dsu.comps;
        else {
            int mid = (l + r) / 2;
            dfs(2 * v, l, mid, ans);
            dfs(2 * v + 1, mid + 1, r, ans);
        }
        for (query q : t[v]) {
            if (q.united)
                dsu.rollback();
        }
    }

    vector<int> solve() {
        vector<int> ans(T);
        dfs(1, 0, T - 1, ans);
        return ans;
    }
};
```

## مسائل

- [Codeforces - Connect and Disconnect](https://codeforces.com/gym/100551/problem/A)
- [Codeforces - Addition on Segments](https://codeforces.com/contest/981/problem/E)
- [Codeforces - Extending Set of Points](https://codeforces.com/contest/1140/problem/F)