---
tags:
  - AI Translated
e_maxx_link: determinant-kraut
---

# محاسبه دترمینان به روش Kraut در $O(N^3)$

در این مقاله، روش Kraut برای محاسبه دترمینان ماتریس را شرح می‌دهیم که در مرتبه زمانی $O(N^3)$ عمل می‌کند.

الگوریتم Kraut، ماتریس $A$ را به صورت $A = LU$ تجزیه می‌کند که در آن $L$ یک ماتریس پایین‌مثلثی و $U$ یک ماتریس بالا‌مثلثی است. بدون از دست دادن کلیت مسئله، می‌توانیم فرض کنیم که تمام درایه‌های روی قطر اصلی ماتریس $L$ برابر با ۱ هستند. پس از یافتن این ماتریس‌ها، محاسبه دترمینان $A$ آسان خواهد بود: دترمینان برابر است با حاصل‌ضرب تمام درایه‌های روی قطر اصلی ماتریس $U$.

قضیه‌ای وجود دارد که بیان می‌کند هر ماتریس معکوس‌پذیری، تجزیه LU دارد و این تجزیه منحصر به فرد است، اگر و تنها اگر تمام کهادهای اصلی آن غیرصفر باشند. ما فقط تجزیه‌ای را در نظر می‌گیریم که در آن قطر اصلی ماتریس $L$ از یک‌ها تشکیل شده باشد.

فرض کنید $A$ ماتریس و $N$ ابعاد آن باشد. درایه‌های ماتریس‌های $L$ و $U$ را طی مراحل زیر به دست می‌آوریم:

۱. برای هر $i = 1, 2, ..., N$، قرار دهید $L_{i i} = 1$.
۲. به ازای هر $j$ از ۱ تا $N$:
      - برای هر $i$ از ۱ تا $j$:
        
        \[U_{ij} = A_{ij} - \sum_{k=1}^{i-1} L_{ik} \cdot U_{kj}\]
 
      - سپس، برای هر $i$ از $j+1$ تا $N$:
 
        \[L_{ij} = \frac{1}{U_{jj}} \left(A_{ij} - \sum_{k=1}^{j-1} L_{ik} \cdot U_{kj} \right).\]

## پیاده‌سازی

```java
static BigInteger det (BigDecimal a [][], int n) {
	try {

	for (int i=0; i<n; i++) {
		boolean nonzero = false;
		for (int j=0; j<n; j++)
			if (a[i][j].compareTo (new BigDecimal (BigInteger.ZERO)) > 0)
				nonzero = true;
		if (!nonzero)
			return BigInteger.ZERO;
	}

	BigDecimal scaling [] = new BigDecimal [n];
	for (int i=0; i<n; i++) {
		BigDecimal big = new BigDecimal (BigInteger.ZERO);
		for (int j=0; j<n; j++)
			if (a[i][j].abs().compareTo (big) > 0)
				big = a[i][j].abs();
		scaling[i] = (new BigDecimal (BigInteger.ONE)) .divide
			(big, 100, BigDecimal.ROUND_HALF_EVEN);
	}

	int sign = 1;

	for (int j=0; j<n; j++) {
		for (int i=0; i<j; i++) {
			BigDecimal sum = a[i][j];
			for (int k=0; k<i; k++)
				sum = sum.subtract (a[i][k].multiply (a[k][j]));
			a[i][j] = sum;
		}

		BigDecimal big = new BigDecimal (BigInteger.ZERO);
		int imax = -1;
		for (int i=j; i<n; i++) {
			BigDecimal sum = a[i][j];
			for (int k=0; k<j; k++)
				sum = sum.subtract (a[i][k].multiply (a[k][j]));
			a[i][j] = sum;
			BigDecimal cur = sum.abs();
			cur = cur.multiply (scaling[i]);
			if (cur.compareTo (big) >= 0) {
				big = cur;
				imax = i;
			}
		}

		if (j != imax) {
			for (int k=0; k<n; k++) {
				BigDecimal t = a[j][k];
				a[j][k] = a[imax][k];
				a[imax][k] = t;
			}

			BigDecimal t = scaling[imax];
			scaling[imax] = scaling[j];
			scaling[j] = t;

			sign = -sign;
		}

		if (j != n-1)
			for (int i=j+1; i<n; i++)
				a[i][j] = a[i][j].divide
					(a[j][j], 100, BigDecimal.ROUND_HALF_EVEN);

	}

	BigDecimal result = new BigDecimal (1);
	if (sign == -1)
		result = result.negate();
	for (int i=0; i<n; i++)
		result = result.multiply (a[i][i]);

	return result.divide
		(BigDecimal.valueOf(1), 0, BigDecimal.ROUND_HALF_EVEN).toBigInteger();
	}
	catch (Exception e) {
		return BigInteger.ZERO;
	}
}
```