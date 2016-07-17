#
# 斐波那契数列

## 36.1 Example #1

```
#include <stdio.h>

void fib (int a, int b, int limit)
{
	printf ("%d\n", a+b);
	if (a+b > limit)
		return;
	fib (b, a+b, limit);
};

int main()
{
	printf ("0\n1\n1\n");
	fib (1, 1, 20);
};

```
