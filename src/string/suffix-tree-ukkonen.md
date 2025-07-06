---
tags:
  - AI Translated
e_maxx_link: suffix-tree-ukkonen
---

# درخت پسوندی. الگوریتم Ukkonen

*این مقاله یک نوشتار خرد است و حاوی هیچ توضیحی نیست. برای مطالعه توضیحات الگوریتم، به منابع دیگر مانند کتاب [Algorithms on Strings, Trees, and Sequences](https://www.cs.cmu.edu/afs/cs/project/pscico-guyb/realworld/www/slidesF06/cmuonly/gusfield.pdf) نوشته Dan Gusfield مراجعه کنید.*

این الگوریتم یک درخت پسوندی برای یک رشته‌ی داده شده $s$ به طول $n$ در زمان $O(n\log(k))$ می‌سازد که در آن $k$ اندازه‌ی الفبا است (اگر $k$ ثابت در نظر گرفته شود، پیچیدگی زمانی خطی خواهد بود).

ورودی الگوریتم، رشته‌ی $s$ و طول آن $n$ است که به عنوان متغیرهای سراسری (global) پاس داده می‌شوند.

تابع اصلی `build_tree` درخت پسوندی را می‌سازد. این درخت به صورت آرایه‌ای از ساختارهای `node` ذخیره می‌شود که `node[0]` ریشه‌ی درخت است.

برای ساده‌سازی کد، یال‌ها نیز در همین ساختارها ذخیره می‌شوند: برای هر گره، ساختار `node` آن اطلاعات مربوط به یال بین آن و والدش را ذخیره می‌کند. در مجموع هر `node` اطلاعات زیر را ذخیره می‌کند:

*   `(l, r)` - کران‌های چپ و راست زیررشته‌ی `s[l..r-1]` که متناظر با یال ورودی به این گره است،
*   `par` - گره والد،
*   `link` - پیوند پسوندی (suffix link)،
*   `next` - لیست یال‌های خروجی از این گره.

```cpp
string s;
int n;
 
struct node {
	int l, r, par, link;
	map<char,int> next;
 
	node (int l=0, int r=0, int par=-1)
		: l(l), r(r), par(par), link(-1) {}
	int len()  {  return r - l;  }
	int &get (char c) {
		if (!next.count(c))  next[c] = -1;
		return next[c];
	}
};
node t[MAXN];
int sz;
 
struct state {
	int v, pos;
	state (int v, int pos) : v(v), pos(pos)  {}
};
state ptr (0, 0);
 
state go (state st, int l, int r) {
	while (l < r)
		if (st.pos == t[st.v].len()) {
			st = state (t[st.v].get( s[l] ), 0);
			if (st.v == -1)  return st;
		}
		else {
			if (s[ t[st.v].l + st.pos ] != s[l])
				return state (-1, -1);
			if (r-l < t[st.v].len() - st.pos)
				return state (st.v, st.pos + r-l);
			l += t[st.v].len() - st.pos;
			st.pos = t[st.v].len();
		}
	return st;
}
 
int split (state st) {
	if (st.pos == t[st.v].len())
		return st.v;
	if (st.pos == 0)
		return t[st.v].par;
	node v = t[st.v];
	int id = sz++;
	t[id] = node (v.l, v.l+st.pos, v.par);
	t[v.par].get( s[v.l] ) = id;
	t[id].get( s[v.l+st.pos] ) = st.v;
	t[st.v].par = id;
	t[st.v].l += st.pos;
	return id;
}
 
int get_link (int v) {
	if (t[v].link != -1)  return t[v].link;
	if (t[v].par == -1)  return 0;
	int to = get_link (t[v].par);
	return t[v].link = split (go (state(to,t[to].len()), t[v].l + (t[v].par==0), t[v].r));
}
 
void tree_extend (int pos) {
	for(;;) {
		state nptr = go (ptr, pos, pos+1);
		if (nptr.v != -1) {
			ptr = nptr;
			return;
		}
 
		int mid = split (ptr);
		int leaf = sz++;
		t[leaf] = node (pos, n, mid);
		t[mid].get( s[pos] ) = leaf;
 
		ptr.v = get_link (mid);
		ptr.pos = t[ptr.v].len();
		if (!mid)  break;
	}
}
 
void build_tree() {
	sz = 1;
	for (int i=0; i<n; ++i)
		tree_extend (i);
}
```

## پیاده‌سازی فشرده

این پیاده‌سازی فشرده توسط [freopen](http://codeforces.com/profile/freopen) پیشنهاد شده است.

```cpp
const int N=1000000,INF=1000000000;
string a;
int t[N][26],l[N],r[N],p[N],s[N],tv,tp,ts,la;
 
void ukkadd (int c) {
	suff:;
	if (r[tv]<tp) {
		if (t[tv][c]==-1) { t[tv][c]=ts;  l[ts]=la;
			p[ts++]=tv;  tv=s[tv];  tp=r[tv]+1;  goto suff; }
		tv=t[tv][c]; tp=l[tv];
	}
	if (tp==-1 || c==a[tp]-'a') tp++; else {
		l[ts+1]=la;  p[ts+1]=ts;
		l[ts]=l[tv];  r[ts]=tp-1;  p[ts]=p[tv];  t[ts][c]=ts+1;  t[ts][a[tp]-'a']=tv;
		l[tv]=tp;  p[tv]=ts;  t[p[ts]][a[l[ts]]-'a']=ts;  ts+=2;
		tv=s[p[ts-2]];  tp=l[ts-2];
		while (tp<=r[ts-2]) {  tv=t[tv][a[tp]-'a'];  tp+=r[tv]-l[tv]+1;}
		if (tp==r[ts-2]+1)  s[ts-2]=tv;  else s[ts-2]=ts; 
		tp=r[tv]-(tp-r[ts-2])+2;  goto suff;
	}
}
 
void build() {
	ts=2;
	tv=0;
	tp=0;
	fill(r,r+N,(int)a.size()-1);
	s[0]=1;
	l[0]=-1;
	r[0]=-1;
	l[1]=-1;
	r[1]=-1;
	memset (t, -1, sizeof t);
	fill(t[1],t[1]+26,0);
	for (la=0; la<(int)a.size(); ++la)
		ukkadd (a[la]-'a');
}
```

همان کد با توضیحات:

```cpp
const int N=1000000,    // بیشترین تعداد ممکن گره‌ها در درخت پسوندی
	INF=1000000000; // ثابت بی‌نهایت
string a;       // رشته ورودی که درخت پسوندی برای آن ساخته می‌شود
int t[N][26],   // آرایه انتقال‌ها (حالت، حرف)
	l[N],   // کران چپ...
	r[N],   // ...و راستِ زیررشته‌ای از a که متناظر با یال ورودی است
	p[N],   // والد گره
	s[N],   // پیوند پسوندی
	tv,     // گره پسوند فعلی (اگر وسط یک یال باشیم، گره پایینیِ یال)
	tp,     // موقعیت در رشته که متناظر با موقعیت روی یال است (بین l[tv] و r[tv]، شامل خودشان)
	ts,     // تعداد گره‌ها
	la;     // کاراکتر فعلی در رشته
 
void ukkadd(int c) { // اضافه کردن کاراکتر c به درخت
	suff:;      // بعد از هر انتقال به پسوند به اینجا برمی‌گردیم (و دوباره کاراکتر را اضافه می‌کنیم)
	if (r[tv]<tp) { // بررسی می‌کند که آیا هنوز در محدوده‌ی یال فعلی هستیم یا خیر
		// اگر نیستیم، یال بعدی را پیدا می‌کنیم. اگر وجود نداشت، یک برگ ایجاد کرده و به درخت اضافه می‌کنیم
		if (t[tv][c]==-1) {t[tv][c]=ts;l[ts]=la;p[ts++]=tv;tv=s[tv];tp=r[tv]+1;goto suff;}
		tv=t[tv][c];tp=l[tv];
	} // در غیر این صورت فقط به یال بعدی می‌رویم
	if (tp==-1 || c==a[tp]-'a')
		tp++; // اگر حرف روی یال با c برابر باشد، در امتداد آن یال پایین می‌رویم
	else { 
		// در غیر این صورت، یال را با گره میانی ts به دو قسمت تقسیم می‌کنیم
		l[ts]=l[tv];r[ts]=tp-1;p[ts]=p[tv];t[ts][a[tp]-'a']=tv;
		// برگ ts+1 را اضافه می‌کنیم. این برگ متناظر با انتقال از طریق c است.
		t[ts][c]=ts+1;l[ts+1]=la;p[ts+1]=ts;
		// اطلاعات گره فعلی را به‌روزرسانی می‌کنیم - به یاد داشته باشید که ts را به عنوان والد tv علامت‌گذاری کنید
		l[tv]=tp;p[tv]=ts;t[p[ts]][a[l[ts]]-'a']=ts;ts+=2;
		// برای پایین رفتن آماده می‌شویم
		// tp مشخص می‌کند که در پسوند فعلی کجا هستیم
		tv=s[p[ts-2]];tp=l[ts-2];
		// تا زمانی که پسوند فعلی تمام نشده، پایین می‌رویم
		while (tp<=r[ts-2]) {tv=t[tv][a[tp]-'a'];tp+=r[tv]-l[tv]+1;}
		// اگر در یک گره هستیم، یک پیوند پسوندی به آن اضافه می‌کنیم، در غیر این صورت پیوند را به ts اضافه می‌کنیم
		// (ما ts را در تکرار بعدی ایجاد خواهیم کرد).
		if (tp==r[ts-2]+1) s[ts-2]=tv; else s[ts-2]=ts; 
		// tp را به یال جدید اضافه کرده و برای اضافه کردن حرف به پسوند برمی‌گردیم
		tp=r[tv]-(tp-r[ts-2])+2;goto suff;
	}
}
 
void build() {
	ts=2;
	tv=0;
	tp=0;
	fill(r,r+N,(int)a.size()-1);
	// داده‌ها را برای ریشه درخت مقداردهی اولیه می‌کنیم
	s[0]=1;
	l[0]=-1;
	r[0]=-1;
	l[1]=-1;
	r[1]=-1;
	memset (t, -1, sizeof t);
	fill(t[1],t[1]+26,0);
	// متن را حرف به حرف به درخت اضافه می‌کنیم
	for (la=0; la<(int)a.size(); ++la)
		ukkadd (a[la]-'a');
}
```

## مسائل تمرینی

*   [UVA 10679 - I Love Strings!!!](http://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1620)