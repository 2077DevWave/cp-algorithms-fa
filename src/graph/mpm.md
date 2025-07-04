---
tags:
  - AI Translated
e_maxx_link: mpm
---

# جریان بیشینه - الگوریتم MPM

الگوریتم MPM (مخفف Malhotra, Pramodh-Kumar and Maheshwari) مسئله جریان بیشینه را در زمان $O(V^3)$ حل می‌کند. این الگوریتم شبیه به [الگوریتم دینیک](dinic.md) است.

## الگوریتم

مانند الگوریتم دینیک، MPM در فازهایی اجرا می‌شود که طی هر فاز، جریان مسدودکننده را در شبکه لایه‌ایِ گراف باقیمانده $G$ پیدا می‌کنیم.
تفاوت اصلی آن با الگوریتم دینیک در نحوه یافتن جریان مسدودکننده است.
شبکه لایه‌ای $L$ را در نظر بگیرید.
برای هر گره، _پتانسیل داخلی_ و _پتانسیل خارجی_ آن را به صورت زیر تعریف می‌کنیم:

$$\begin{align}
p_{in}(v) &= \sum\limits_{(u, v)\in L}(c(u, v) - f(u, v)) \\\\
p_{out}(v) &= \sum\limits_{(v, u)\in L}(c(v, u) - f(v, u))
\end{align}$$

همچنین، قرار می‌دهیم $p_{in}(s) = p_{out}(t) = \infty$.
با داشتن $p_{in}$ و $p_{out}$، _پتانسیل_ را به صورت $p(v) = min(p_{in}(v), p_{out}(v))$ تعریف می‌کنیم.
گره $r$ را یک _گره مرجع_ می‌نامیم اگر $p(r) = min\{p(v)\}$ باشد.
یک گره مرجع $r$ را در نظر بگیرید.
ادعا می‌کنیم که جریان را می‌توان به اندازه‌ی $p(r)$ افزایش داد به طوری که $p(r)$ برابر با $0$ شود.
این ادعا درست است زیرا $L$ غیرمدور است، پس می‌توانیم جریان را از $r$ از طریق یال‌های خروجی‌اش هدایت کنیم (push) و این جریان به $t$ خواهد رسید. دلیل این امر آن است که هر گره پتانسیل خارجی کافی برای هدایت جریانی که به آن می‌رسد را دارد.
به طور مشابه، می‌توانیم جریان را از $s$ بیرون بکشیم (pull).
ساخت جریان مسدودکننده بر اساس این واقعیت است.
در هر تکرار، یک گره مرجع پیدا کرده و جریان را از $s$ به $t$ از طریق $r$ عبور می‌دهیم.
این فرآیند را می‌توان با BFS شبیه‌سازی کرد.
تمام یال‌های کاملاً اشباع‌شده را می‌توان از $L$ حذف کرد، زیرا در ادامه این فاز به هر حال استفاده نخواهند شد.
به همین ترتیب، تمام گره‌های به جز $s$ و $t$ که یال ورودی یا خروجی ندارند را نیز می‌توان حذف کرد.

هر فاز در زمان $O(V^2)$ اجرا می‌شود، زیرا حداکثر $V$ تکرار وجود دارد (چون حداقل گره مرجع انتخاب‌شده حذف می‌شود)، و در هر تکرار، تمام یال‌هایی که از آنها عبور کرده‌ایم به جز حداکثر $V$ یال را حذف می‌کنیم.
در مجموع، به $O(V^2 + E) = O(V^2)$ می‌رسیم.
از آنجایی که کمتر از $V$ فاز وجود دارد (اثبات را [اینجا](dinic.md) ببینید)، الگوریتم MPM در مجموع در زمان $O(V^3)$ اجرا می‌شود.

## پیاده‌سازی

```{.cpp file=mpm}
struct MPM{
    struct FlowEdge{
        int v, u;
        long long cap, flow;
        FlowEdge(){}
        FlowEdge(int _v, int _u, long long _cap, long long _flow)
            : v(_v), u(_u), cap(_cap), flow(_flow){}
        FlowEdge(int _v, int _u, long long _cap)
            : v(_v), u(_u), cap(_cap), flow(0ll){}
    };
    const long long flow_inf = 1e18;
    vector<FlowEdge> edges;
    vector<char> alive;
    vector<long long> pin, pout;
    vector<list<int> > in, out;
    vector<vector<int> > adj;
    vector<long long> ex;
    int n, m = 0;
    int s, t;
    vector<int> level;
    vector<int> q;
    int qh, qt;
    void resize(int _n){
        n = _n;
        ex.resize(n);
        q.resize(n);
        pin.resize(n);
        pout.resize(n);
        adj.resize(n);
        level.resize(n);
        in.resize(n);
        out.resize(n);
    }
    MPM(){}
    MPM(int _n, int _s, int _t){resize(_n); s = _s; t = _t;}
    void add_edge(int v, int u, long long cap){
        edges.push_back(FlowEdge(v, u, cap));
        edges.push_back(FlowEdge(u, v, 0));
        adj[v].push_back(m);
        adj[u].push_back(m + 1);
        m += 2;
    }
    bool bfs(){
        while(qh < qt){
            int v = q[qh++];
            for(int id : adj[v]){
                if(edges[id].cap - edges[id].flow < 1)continue;
                if(level[edges[id].u] != -1)continue;
                level[edges[id].u] = level[v] + 1;
                q[qt++] = edges[id].u;
            }
        }
        return level[t] != -1;
    }
    long long pot(int v){
        return min(pin[v], pout[v]);
    }
    void remove_node(int v){
        for(int i : in[v]){
            int u = edges[i].v;
            auto it = find(out[u].begin(), out[u].end(), i);
            out[u].erase(it);
            pout[u] -= edges[i].cap - edges[i].flow;
        }
        for(int i : out[v]){
            int u = edges[i].u;
            auto it = find(in[u].begin(), in[u].end(), i);
            in[u].erase(it);
            pin[u] -= edges[i].cap - edges[i].flow;
        }
    }
    void push(int from, int to, long long f, bool forw){
        qh = qt = 0;
        ex.assign(n, 0);
        ex[from] = f;
        q[qt++] = from;
        while(qh < qt){
            int v = q[qh++];
            if(v == to)
                break;
            long long must = ex[v];
            auto it = forw ? out[v].begin() : in[v].begin();
            while(true){
                int u = forw ? edges[*it].u : edges[*it].v;
                long long pushed = min(must, edges[*it].cap - edges[*it].flow);
                if(pushed == 0)break;
                if(forw){
                    pout[v] -= pushed;
                    pin[u] -= pushed;
                }
                else{
                    pin[v] -= pushed;
                    pout[u] -= pushed;
                }
                if(ex[u] == 0)
                    q[qt++] = u;
                ex[u] += pushed;
                edges[*it].flow += pushed;
                edges[(*it)^1].flow -= pushed;
                must -= pushed;
                if(edges[*it].cap - edges[*it].flow == 0){
                    auto jt = it;
                    ++jt;
                    if(forw){
                        in[u].erase(find(in[u].begin(), in[u].end(), *it));
                        out[v].erase(it);
                    }
                    else{
                        out[u].erase(find(out[u].begin(), out[u].end(), *it));
                        in[v].erase(it);
                    }
                    it = jt;
                }
                else break;
                if(!must)break;
            }
        }
    }
    long long flow(){
        long long ans = 0;
        while(true){
            pin.assign(n, 0);
            pout.assign(n, 0);
            level.assign(n, -1);
            alive.assign(n, true);
            level[s] = 0;
            qh = 0; qt = 1;
            q[0] = s;
            if(!bfs())
                break;
            for(int i = 0; i < n; i++){
                out[i].clear();
                in[i].clear();
            }
            for(int i = 0; i < m; i++){
                if(edges[i].cap - edges[i].flow == 0)
                    continue;
                int v = edges[i].v, u = edges[i].u;
                if(level[v] + 1 == level[u] && (level[u] < level[t] || u == t)){
                    in[u].push_back(i);
                    out[v].push_back(i);
                    pin[u] += edges[i].cap - edges[i].flow;
                    pout[v] += edges[i].cap - edges[i].flow;
                }
            }
            pin[s] = pout[t] = flow_inf;
            while(true){
                int v = -1;
                for(int i = 0; i < n; i++){
                    if(!alive[i])continue;
                    if(v == -1 || pot(i) < pot(v))
                        v = i;
                }
                if(v == -1)
                    break;
                if(pot(v) == 0){
                    alive[v] = false;
                    remove_node(v);
                    continue;
                }
                long long f = pot(v);
                ans += f;
                push(v, s, f, false);
                push(v, t, f, true);
                alive[v] = false;
                remove_node(v);
            }
        }
        return ans;
    }
};
```