---
title: Codeforces1622D - Shuffle - 组合数学
date: 2022-01-04 13:43:03
tags: 算法竞赛
---

**题目链接：** https://codeforces.com/contest/1622/problem/D

**题意：** 给定长度为 $n$ 的`01`字符串 $s$，你可以随机选取一段仅包含恰好 $k$ 个`1`的子串 $s[l\cdots r]$，任意调整其中各个字符的位置生成新的字符串 $s'$。提问可以生成多少种不同的新串？答案取模 $998244353$。
- $2\le n\le 5\ 000$
- $0\le k\le n$

**题解：** 直接做比较困难，考虑枚举变化的子串的左端点 $l$ 和右端点 $r$，那么这个区间只会出现在 $1\le l\le r\le n$ 且计数不会重复和遗漏。那么首先要将端点两侧的`0`和`1`反转，假设还剩下 $x$ 个`1`可以用，那么剩余的可能方案数就是 $\tbinom{r-l-1}{x}$。

也存在一些特殊情况：
- 什么都不改的方案数是 $1$；
- 若从区间 $[1,n]$ 的`1`个数也不满 $k$ 个，则无法触发重排条件。

**代码：**
```c++
#include <bits/stdc++.h>
using namespace std;
const int mod = 998244353;
const int maxn = 5005;
int C[maxn][maxn];
char s[maxn];
int sum[maxn];
int main() {
	int n, k;
	scanf("%d%d", &n, &k);
	C[0][0] = 1;
	for (int i = 1; i <= n; i++) {
		C[i][0] = C[i][i] = 1;
		for (int j = 1; j < i; j++) {
			C[i][j] = (C[i-1][j] + C[i-1][j-1]) % mod;
		}
	}
	scanf("%s", s+1);
	sum[0] = 0;
	for (int i = 1; i <= n; i++) {
		sum[i] = sum[i-1] + (s[i] == '1');
	}
	if (sum[n] < k) {
		puts("1");
		exit(0);
	}
	int ans = 1;
	for (int i = 1; i < n; i++) {
		for (int j = i+1; j <= n; j++) {
			int cnt = sum[j] - sum[i-1];
			if (cnt > k) continue;
			int x = j - i - 1;
			if (s[i] == '0') cnt--;
			if (s[j] == '0') cnt--;
			if (cnt <= x && cnt >= 0 && x >= 0) {
				ans = (ans + C[x][cnt]) % mod;
			}
		}
	}
	printf("%d\n", ans);
	return 0;
}
```