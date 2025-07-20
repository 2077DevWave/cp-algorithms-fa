---
tags:
  - Fully Translated
e_maxx_link: deleting_in_log_n
---

# چطوری توی دیتا استراکچرها با هزینه $O(T(n)\log n)$ حذف کنیم؟

رفیق، فرض کن یه دیتا استراکچر داری که اضافه کردن یه عنصر جدید توش $O(T(n))$ طول می‌کشه. خیلی هم عالی. حالا اگه بخوایم حذف کردن رو هم بهش اضافه کنیم چی؟

اینجا می‌خوام یه تکنیک خیلی خفن رو بهت یاد بدم که باهاش می‌تونی عملیات حذف رو به صورت آفلاین (offline) و با پیچیدگی زمانی $O(T(n)\log n)$ انجام بدی.

## الگوریتم چطوری کار می‌کنه؟

ببین، هر عنصری که اضافه می‌کنی، یه «دوره زندگی» داره. از وقتی که اضافه‌اش می‌کنی تا وقتی که حذفش می‌کنی، می‌گیم اون عنصر «زنده» است. این بازه‌ی زمانی، کلید حل مسئله است.

حالا، کلک کار اینجاست: ما میایم روی تایم‌لاینِ کوئری‌هامون یه سگمنت تری (segment tree) می‌سازیم.

اون بازه‌ی زمانی که هر عنصر زنده بود رو یادت میاد؟ حالا اون بازه رو روی این سگمنت تری میندازیم. این کار باعث میشه هر بازه به $O(\log n)$ تا نود توی درخت تقسیم بشه.

هر کوئری‌ای هم که داشتیم (مثلاً بپرسیم "الان وضعیت دیتا استراکچر چطوره؟")، می‌ذاریمش توی برگِ مربوط به زمانِ خودش توی سگمنت تری.

خب، حالا برای اینکه به همه‌ی کوئری‌ها جواب بدیم، یه DFS ساده روی این سگمنت تری می‌زنیم.

*   وقتی با DFS وارد یه گره (node) میشیم، تمام عناصری که به اون گره تعلق دارن (یعنی بازه‌ی زندگیشون این گره رو پوشش میده) رو به دیتا استراکچرمون **اضافه** می‌کنیم.
*   بعدش، میریم سراغ بچه‌های اون گره. اگه به برگ رسیدیم، یعنی به یه زمان خاص رسیدیم، پس به کوئری‌هایی که توی اون برگ منتظر بودن جواب میدیم.
*   و نکته‌ی کلیدی آخر: وقتی کارمون با یه گره و بچه‌هاش تموم شد و داشتیم ازش برمی‌گشتیم (موقع خروج از تابع DFS)، باید اون تغییراتی که اول کار داده بودیم رو **لغو (undo)** کنیم.

یه نکته‌ی باحال اینه که اگه تغییر دادن دیتا استراکچرت $O(T(n))$ طول می‌کشه، می‌تونی همه‌ی تغییرات رو توی یه استک (stack) از تغییرات نگه داری و بعداً با همون هزینه، یعنی $O(T(n))$، تغییرات رو به عقب برگردونی (roll back کنی).

فقط حواست باشه، این تکنیک rollback با پیچیدگی زمانی سرشکن (amortized complexity) خیلی جور در نمیاد و معمولاً خرابش می‌کنه. پس اگه دیتا استراکچرت پیچیدگی سرشکن داره، باید با احتیاط از این روش استفاده کنی.

## نکات تکمیلی

این ایده‌ی ساختن سگمنت تری روی بازه‌های زمانی خیلی قویه و فقط برای دیتا استراکچرها نیست. برای خیلی مسائل دیگه هم که یه چیزی برای یه مدتی «فعال» یا «معتبر» هست، می‌تونی ازش استفاده کنی.

مثلاً، یه نگاهی به مسائل زیر بنداز تا دستت بیاد.

## نمونه کد

اینجا یه پیاده‌سازی برای مسئله‌ی معروف [همبندی پویا (dynamic connectivity)](https://en.wikipedia.org/wiki/Dynamic_connectivity) داریم. با این کد می‌تونی یال اضافه کنی، یال حذف کنی و تعداد مؤلفه‌های همبند گراف رو هم بشماری.

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

## مسائل برای تمرین

اگه دوست داری دستت رو با این تکنیک گرم کنی، این چند تا مسئله برای تمرین عالین:

- [Codeforces - Connect and Disconnect](https://codeforces.com/gym/100551/problem/A)
- [Codeforces - Addition on Segments](https://codeforces.com/contest/981/problem/E)
- [Codeforces - Extending Set of Points](https://codeforces.com/contest/1140/problem/F)