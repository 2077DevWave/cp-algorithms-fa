---
tags:
  - AI Translated
e_maxx_link: segment-to-line
---

# یافتن معادله‌ی خط برای یک پاره‌خط

مسئله این است: با داشتن مختصات دو سر یک پاره‌خط، معادله‌ی خطی را که از آن عبور می‌کند، به دست آوریم.

فرض می‌کنیم که پاره‌خط غیرتبهگن (non-degenerate) است، یعنی طولی بزرگتر از صفر دارد (در غیر این صورت، البته، بی‌نهایت خط مختلف از آن عبور می‌کند).

### حالت دو بعدی

فرض کنید پاره‌خط داده‌شده $PQ$ باشد، یعنی مختصات دو سر آن $P_x, P_y, Q_x, Q_y$ معلوم است.

لازم است **معادله‌ی یک خط در صفحه** را که از این پاره‌خط عبور می‌کند، به دست آوریم، یعنی ضرایب $A, B, C$ را در معادله‌ی خط زیر پیدا کنیم:

$$A x + B y + C = 0.$$

توجه داشته باشید که برای سه‌تایی‌های مطلوب $(A, B, C)$ **بی‌نهایت** جواب وجود دارد که پاره‌خط داده‌شده را توصیف می‌کنند: شما می‌توانید هر سه ضریب را در یک عدد غیرصفر دلخواه ضرب کنید و همان خط راست را به دست آورید. بنابراین، وظیفه‌ی ما پیدا کردن یکی از این سه‌تایی‌ها است.

به راحتی می‌توان (با جایگذاری این عبارات و مختصات نقاط $P$ و $Q$ در معادله‌ی خط راست) تأیید کرد که مجموعه‌ی ضرایب زیر مناسب است:

$$\begin{align}
A &= P_y - Q_y, \\
B &= Q_x - P_x, \\
C &= - A P_x - B P_y.
\end{align}$$

### حالت اعداد صحیح

یک مزیت مهم این روش برای به دست آوردن معادله‌ی خط این است که اگر مختصات دو سر پاره‌خط صحیح باشند، ضرایب به دست آمده نیز **صحیح** خواهند بود. در برخی موارد، این ویژگی به ما اجازه می‌دهد تا عملیات هندسی را بدون توسل به اعداد حقیقی انجام دهیم.

با این حال، یک ایراد کوچک وجود دارد: برای یک خط راست ممکن است سه‌تایی‌های متفاوتی از ضرایب به دست آید. برای جلوگیری از این مشکل، بدون اینکه از ضرایب صحیح خارج شویم، می‌توانید تکنیک زیر را که اغلب **استانداردسازی (rationing)** نامیده می‌شود، به کار ببرید. [بزرگترین مقسوم‌علیه مشترک (ب.م.م)](../algebra/euclid-algorithm.md) اعداد $| A | , | B | , | C |$ را پیدا کرده، هر سه ضریب را بر آن تقسیم می‌کنیم، و سپس نرمال‌سازی علامت را انجام می‌دهیم: اگر $A <0$ یا $A = 0, B <0$ بود، هر سه ضریب را در $-1$ ضرب می‌کنیم. در نتیجه، به این جمع‌بندی می‌رسیم که برای خطوط راست یکسان، سه‌تایی‌های ضرایب یکسانی به دست می‌آید، که این موضوع بررسی تساوی خطوط راست را آسان می‌کند.

### حالت اعداد حقیقی

هنگام کار با اعداد حقیقی، باید همیشه از خطاها آگاه باشید.

ضرایب $A$ و $B$ از مرتبه‌ی مختصات اولیه خواهند بود و ضریب $C$ از مرتبه‌ی مربع آنها است. این اعداد ممکن است بسیار بزرگ شوند و برای مثال، هنگامی که [خطوط راست را با هم تلاقی می‌دهیم](lines-intersection.md)، آنها حتی بزرگتر هم می‌شوند، که این موضوع می‌تواند منجر به خطاهای گردکردن بزرگی شود، حتی زمانی که مختصات نقاط انتهایی از مرتبه $10^3$ باشند.

بنابراین، هنگام کار با اعداد حقیقی، مطلوب است که فرآیندی به نام **نرمال‌سازی (normalization)** را انجام دهیم که کاری ساده است: یعنی، ضرایب را طوری تنظیم کنیم که $A ^ 2 + B ^ 2 = 1$ باشد. برای انجام این کار، عدد $Z$ را محاسبه می‌کنیم:

$$Z = \sqrt{A ^ 2 + B ^ 2},$$

و هر سه ضریب $A, B, C$ را بر آن تقسیم می‌کنیم.

بنابراین، مرتبه‌ی ضرایب $A$ و $B$ به مرتبه‌ی مختصات ورودی وابسته نخواهد بود و ضریب $C$ هم‌مرتبه با مختصات ورودی خواهد بود. در عمل، این امر منجر به بهبود قابل توجهی در دقت محاسبات می‌شود.

در نهایت، به **مقایسه‌ی** خطوط راست می‌پردازیم. در واقع، پس از چنین نرمال‌سازی، برای یک خط راست یکسان، تنها دو سه‌تایی از ضرایب را می‌توان به دست آورد که فقط در یک ضریب $-1$ با هم تفاوت دارند. بر این اساس، اگر یک نرمال‌سازی اضافی با در نظر گرفتن علامت انجام دهیم (اگر $A < -\varepsilon$ یا $| A | < \varepsilon$ و $B < -\varepsilon$ باشد، ضرایب را در $-1$ ضرب می‌کنیم)، ضرایب حاصل منحصر به فرد خواهند بود.

### حالت سه‌بعدی و چندبعدی

حتی در حالت سه‌بعدی نیز **معادله‌ی ساده‌ای** برای توصیف یک خط راست وجود ندارد (می‌توان آن را به عنوان محل تلاقی دو صفحه تعریف کرد، یعنی یک دستگاه دو معادله‌ای، اما این روشی نامناسب است).

در نتیجه، در حالت‌های سه‌بعدی و چندبعدی باید از **روش پارامتری برای تعریف خط راست** استفاده کنیم، یعنی به صورت یک نقطه $p$ و یک بردار $v$:

$$p + v t, ~~~ t \in \mathbb{R}.$$

یعنی، یک خط راست مجموعه‌ی تمام نقاطی است که می‌توان از نقطه‌ی $p$ با افزودن بردار $v$ با یک ضریب دلخواه به دست آورد.

**ساختن** یک خط راست به شکل پارامتری با استفاده از مختصات دو سر یک پاره‌خط امری بدیهی است. کافی است یک سر پاره‌خط را به عنوان نقطه $p$ و بردارِ سر اول به سر دوم را به عنوان بردار $v$ در نظر بگیریم.