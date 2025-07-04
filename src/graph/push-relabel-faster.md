---
tags:
  - AI Translated
e_maxx_link: push-relabel-faster
---

# جریان بیشینه - روش Push-relabel بهبودیافته

در این مقاله، [روش push-relabel](push-relabel.md) را برای دستیابی به زمان اجرای بهتر، اصلاح می‌کنیم.

## توضیحات

این اصلاح بسیار ساده است:
در مقاله قبلی، رأسی با جریان اضافی را بدون هیچ قانون خاصی انتخاب می‌کردیم.
اما مشخص شده است که اگر همیشه رأس‌هایی با **بیشترین ارتفاع** را انتخاب کرده و عملیات push و relabel را روی آن‌ها اعمال کنیم، پیچیدگی زمانی بهبود می‌یابد.
علاوه بر این، برای انتخاب رأس‌هایی با بیشترین ارتفاع، در واقع نیازی به ساختمان داده‌ی خاصی نداریم. ما به سادگی رأس‌های با بیشترین ارتفاع را در یک لیست ذخیره می‌کنیم و این لیست را پس از پردازش همه‌ی آن‌ها مجدداً محاسبه می‌کنیم (در این صورت رأس‌هایی با ارتفاع کمتر به لیست اضافه می‌شوند)، یا هر زمان که یک رأس جدید با جریان اضافی و ارتفاع بیشتر ظاهر شود (پس از relabel کردن یک رأس)، لیست را به‌روزرسانی می‌کنیم.

با وجود سادگی، این تغییر، پیچیدگی را به میزان قابل توجهی کاهش می‌دهد.
به‌طور دقیق، پیچیدگی الگوریتم حاصل برابر با $O(V E + V^2 \sqrt{E})$ است که در بدترین حالت $O(V^3)$ می‌شود.

این اصلاح توسط Cheriyan و Maheshwari در سال 1989 پیشنهاد شد.

## پیاده‌سازی

```{.cpp file=push_relabel_faster}
const int inf = 1000000000;

int n;
vector<vector<int>> capacity, flow;
vector<int> height, excess;

void push(int u, int v)
{
    int d = min(excess[u], capacity[u][v] - flow[u][v]);
    flow[u][v] += d;
    flow[v][u] -= d;
    excess[u] -= d;
    excess[v] += d;
}

void relabel(int u)
{
    int d = inf;
    for (int i = 0; i < n; i++) {
        if (capacity[u][i] - flow[u][i] > 0)
            d = min(d, height[i]);
    }
    if (d < inf)
        height[u] = d + 1;
}

vector<int> find_max_height_vertices(int s, int t) {
    vector<int> max_height;
    for (int i = 0; i < n; i++) {
        if (i != s && i != t && excess[i] > 0) {
            if (!max_height.empty() && height[i] > height[max_height[0]])
                max_height.clear();
            if (max_height.empty() || height[i] == height[max_height[0]])
                max_height.push_back(i);
        }
    }
    return max_height;
}

int max_flow(int s, int t)
{
    height.assign(n, 0);
    height[s] = n;
    flow.assign(n, vector<int>(n, 0));
    excess.assign(n, 0);
    excess[s] = inf;
    for (int i = 0; i < n; i++) {
        if (i != s)
            push(s, i);
    }

    vector<int> current;
    while (!(current = find_max_height_vertices(s, t)).empty()) {
        for (int i : current) {
            bool pushed = false;
            for (int j = 0; j < n && excess[i]; j++) {
                if (capacity[i][j] - flow[i][j] > 0 && height[i] == height[j] + 1) {
                    push(i, j);
                    pushed = true;
                }
            }
            if (!pushed) {
                relabel(i);
                break;
            }
        }
    }

    return excess[t];
}
```