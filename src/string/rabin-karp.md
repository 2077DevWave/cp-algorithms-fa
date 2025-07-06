---
tags:
  - AI Translated
e_maxx_link: rabin-karp
---

# الگوریتم رابین-کارپ برای تطابق رشته

این الگوریتم بر پایه مفهوم درهم‌سازی (hashing) است، بنابراین اگر با درهم‌سازی رشته آشنا نیستید، به مقاله [درهم‌سازی رشته](string-hashing.md) مراجعه کنید.

این الگوریتم در سال ۱۹۸۷ توسط رابین و کارپ ارائه شد.

مسئله: با داشتن دو رشته - یک الگوی $s$ و یک متن $t$، بررسی کنید که آیا الگو در متن ظاهر می‌شود یا خیر و اگر ظاهر می‌شود، تمام رخدادهای آن را در زمان $O(|s| + |t|)$ پیدا کنید.

الگوریتم: مقدار هش را برای الگوی $s$ محاسبه کنید.
مقادیر هش را برای تمام پیشوندهای متن $t$ محاسبه کنید.
اکنون، با استفاده از هش‌های محاسبه‌شده، می‌توانیم یک زیررشته به طول $|s|$ را در زمان ثابت با $s$ مقایسه کنیم.
بنابراین، هر زیررشته به طول $|s|$ را با الگو مقایسه کنید. این کار در مجموع $O(|t|)$ زمان می‌برد.
در نتیجه، پیچیدگی زمانی نهایی الگوریتم برابر با $O(|t| + |s|)$ است: $O(|s|)$ برای محاسبه هش الگو و $O(|t|)$ برای مقایسه هر زیررشته به طول $|s|$ با الگو نیاز است.

## پیاده‌سازی
```{.cpp file=rabin_karp}
vector<int> rabin_karp(string const& s, string const& t) {
    const int p = 31; 
    const int m = 1e9 + 9;
    int S = s.size(), T = t.size();

    vector<long long> p_pow(max(S, T)); 
    p_pow[0] = 1; 
    for (int i = 1; i < (int)p_pow.size(); i++) 
        p_pow[i] = (p_pow[i-1] * p) % m;

    vector<long long> h(T + 1, 0); 
    for (int i = 0; i < T; i++)
        h[i+1] = (h[i] + (t[i] - 'a' + 1) * p_pow[i]) % m; 
    long long h_s = 0; 
    for (int i = 0; i < S; i++) 
        h_s = (h_s + (s[i] - 'a' + 1) * p_pow[i]) % m; 

    vector<int> occurrences;
    for (int i = 0; i + S - 1 < T; i++) {
        long long cur_h = (h[i+S] + m - h[i]) % m;
        if (cur_h == h_s * p_pow[i] % m)
            occurrences.push_back(i);
    }
    return occurrences;
}
```

## مسائل تمرینی

* [SPOJ - Pattern Find](http://www.spoj.com/problems/NAJPF/)
* [Codeforces - Good Substrings](http://codeforces.com/problemset/problem/271/D)
* [Codeforces - Palindromic characteristics](https://codeforces.com/problemset/problem/835/D)
* [Leetcode - Longest Duplicate Substring](https://leetcode.com/problems/longest-duplicate-substring/)