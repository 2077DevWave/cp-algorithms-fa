---
tags:
  - AI Translated
e_maxx_link: search-for-connected-components
---

# جستجو برای مؤلفه‌های همبند در یک گراف

یک گراف غیرجهت‌دار $G$ با $n$ رأس و $m$ یال داده شده است. ما باید تمام مؤلفه‌های همبند آن را پیدا کنیم، یعنی چندین گروه از رأس‌ها که در هر گروه، هر رأس از رأس دیگر قابل دسترسی است و هیچ مسیری بین گروه‌های مختلف وجود ندارد.

## الگوریتمی برای حل مسئله

*   برای حل این مسئله، می‌توانیم از Depth First Search یا Breadth First Search استفاده کنیم.

*   در واقع، ما یک سری پیمایش DFS انجام خواهیم داد: پیمایش اول از رأس اول شروع می‌شود و تمام رأس‌های اولین مؤلفه همبند پیمایش (پیدا) می‌شوند. سپس اولین رأس ملاقات‌نشده از بین رأس‌های باقی‌مانده را پیدا کرده و Depth First Search را روی آن اجرا می‌کنیم و به این ترتیب دومین مؤلفه همبند را پیدا می‌کنیم. و به همین ترتیب ادامه می‌دهیم تا تمام رأس‌ها ملاقات شوند.

*   پیچیدگی زمانی مجانبی کل این الگوریتم $O(n + m)$ است: در واقع، این الگوریتم روی هیچ رأسی دو بار اجرا نخواهد شد، که به این معنی است که هر یال دقیقاً دو بار دیده می‌شود (یک بار از یک سر و بار دیگر از سر دیگر).

## پیاده‌سازی

``` cpp
int n;
vector<vector<int>> adj;
vector<bool> used;
vector<int> comp;

void dfs(int v) {
    used[v] = true ;
    comp.push_back(v);
    for (int u : adj[v]) {
        if (!used[u])
            dfs(u);
    }
}

void find_comps() {
    fill(used.begin(), used.end(), 0);
    for (int v = 0; v < n; ++v) {
        if (!used[v]) {
            comp.clear();
            dfs(v);
            cout << "Component:" ;
            for (int u : comp)
                cout << ' ' << u;
            cout << endl ;
        }
    }
}
```

*   مهم‌ترین تابعی که استفاده می‌شود `find_comps()` است که مؤلفه‌های همبند گراف را پیدا کرده و نمایش می‌دهد.

*   گراف به صورت لیست مجاورت ذخیره می‌شود، یعنی `adj[v]` شامل لیستی از رأس‌هایی است که از رأس `v` به آن‌ها یال وجود دارد.

*   وکتور `comp` شامل لیستی از رأس‌های مؤلفه همبند فعلی است.

## پیاده‌سازی غیربازگشتی کد

توابع با عمق بازگشتی زیاد به طور کلی خوب نیستند.
هر فراخوانی بازگشتی به مقداری حافظه در `stack` نیاز دارد و برنامه‌ها به طور پیش‌فرض فقط مقدار محدودی فضای `stack` دارند.
بنابراین وقتی یک DFS بازگشتی را روی یک گراف همبند با میلیون‌ها رأس اجرا می‌کنید، ممکن است با خطای `stack overflow` مواجه شوید.

همیشه می‌توان یک برنامه بازگشتی را با نگهداری دستی یک ساختمان داده `stack`، به یک برنامه غیربازگشتی تبدیل کرد.
از آنجایی که این ساختمان داده روی `heap` تخصیص داده می‌شود، خطای `stack overflow` رخ نخواهد داد.

```cpp
int n;
vector<vector<int>> adj;
vector<bool> used;
vector<int> comp;

void dfs(int v) {
    stack<int> st;
    st.push(v);
    
    while (!st.empty()) {
        int curr = st.top();
        st.pop();
        if (!used[curr]) {
            used[curr] = true;
            comp.push_back(curr);
            for (int i = adj[curr].size() - 1; i >= 0; i--) {
                st.push(adj[curr][i]);
            }
        }
    }
}

void find_comps() {
    fill(used.begin(), used.end(), 0);
    for (int v = 0; v < n ; ++v) {
        if (!used[v]) {
            comp.clear();
            dfs(v);
            cout << "Component:" ;
            for (int u : comp)
                cout << ' ' << u;
            cout << endl ;
        }
    }
}
```

## مسائل تمرینی
 - [SPOJ: CT23E](http://www.spoj.com/problems/CT23E/)
 - [CODECHEF: GERALD07](https://www.codechef.com/MARCH14/problems/GERALD07)
 - [CSES : Building Roads](https://cses.fi/problemset/task/1666)