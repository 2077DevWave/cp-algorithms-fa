---
tags:
  - AI Translated
e_maxx_link: mst_kruskal_with_dsu
---

# درخت پوشای کمینه - کراسکال با ساختار داده اجتماع مجموعه‌های مجزا (DSU)

برای مطالعه‌ی توضیحات مسئله درخت پوشای کمینه (MST) و الگوریتم کراسکال، ابتدا به [مقاله اصلی الگوریتم کراسکال](mst_kruskal.md) مراجعه کنید.

در این مقاله، ساختار داده ["اجتماع مجموعه‌های مجزا" (Disjoint Set Union)](../data_structures/disjoint_set_union.md) را برای پیاده‌سازی الگوریتم کراسکال بررسی می‌کنیم. این ساختار داده به الگوریتم اجازه می‌دهد تا به پیچیدگی زمانی $O(M \log N)$ دست یابد.

## توضیحات

درست مانند نسخه ساده الگوریتم کراسکال، تمام یال‌های گراف را بر اساس وزنشان به صورت غیر نزولی مرتب می‌کنیم.
سپس هر رأس را از طریق فراخوانی تابع `make_set` در درخت (مجموعه) خودش قرار می‌دهیم. این کار در مجموع $O(N)$ زمان می‌برد.
بر روی تمام یال‌ها (به ترتیب مرتب‌شده) پیمایش می‌کنیم و برای هر یال، با دو فراخوانی `find_set` که هر کدام در $O(1)$ انجام می‌شود، بررسی می‌کنیم که آیا دو سر یال به درخت‌های متفاوتی تعلق دارند یا خیر.
در نهایت، باید دو درخت (مجموعه) را با یکدیگر ادغام کنیم که برای این کار تابع `union_sets` از DSU فراخوانی می‌شود - این عمل نیز در $O(1)$ انجام می‌گیرد.
بنابراین، پیچیدگی زمانی کل برابر با $O(M \log N + N + M)$ = $O(M \log N)$ خواهد بود.

## پیاده‌سازی

در ادامه یک پیاده‌سازی از الگوریتم کراسکال با استفاده از تکنیک Union by Rank آمده است.

```cpp
vector<int> parent, rank;

void make_set(int v) {
    parent[v] = v;
    rank[v] = 0;
}

int find_set(int v) {
    if (v == parent[v])
        return v;
    return parent[v] = find_set(parent[v]);
}

void union_sets(int a, int b) {
    a = find_set(a);
    b = find_set(b);
    if (a != b) {
        if (rank[a] < rank[b])
            swap(a, b);
        parent[b] = a;
        if (rank[a] == rank[b])
            rank[a]++;
    }
}

struct Edge {
    int u, v, weight;
    bool operator<(Edge const& other) {
        return weight < other.weight;
    }
};

int n;
vector<Edge> edges;

int cost = 0;
vector<Edge> result;
parent.resize(n);
rank.resize(n);
for (int i = 0; i < n; i++)
    make_set(i);

sort(edges.begin(), edges.end());

for (Edge e : edges) {
    if (find_set(e.u) != find_set(e.v)) {
        cost += e.weight;
        result.push_back(e);
        union_sets(e.u, e.v);
    }
}
```

توجه: از آنجایی که درخت پوشای کمینه دقیقاً $N-1$ یال خواهد داشت، می‌توانیم حلقه for را به محض پیدا کردن این تعداد یال متوقف کنیم.

## مسائل تمرینی

برای مشاهده لیست مسائل تمرینی مرتبط با این موضوع، به [مقاله اصلی الگوریتم کراسکال](mst_kruskal.md) مراجعه کنید.