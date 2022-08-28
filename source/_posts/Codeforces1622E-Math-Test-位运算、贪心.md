---
title: Codeforces1622E - Math Test - 位运算、贪心
date: 2022-01-05 13:19:16
tags: 算法竞赛
---

**题目链接：** https://codeforces.com/contest/1622/problem/E

**题意：** 学校里有 $n$ 位学生，这次考试包含 $m$ 个题目，作为学校老师，你知道每位学生能够回答出哪些题目（由长度为 $m$ 的`01`串表示，其中第 $i$ 个字符若为`0`则表示该学生不能做出这道题，若为`1`则表示该学生可以做出本道题）。现在要为这 $m$ 道题目分配一个分值，要求每道题的分值是 $[1,m]$ 之间的一个整数，且分值各不相同。已知每个学生期望获得的分数为 $x_i$，实际获得的分数为 $r_i$，求 $\sum\limits_{i=0}^{n}|x_i-r_i|$ 最大化前提下的一种合法的分值分配方案。
- $1\le n\le 10$
- $1\le m\le 10\ 000$

**题解：** 首先要想到把绝对值拆开，对于每一项 $|x_i-r_i|$，它的取值要么是 $x_i-r_i\ (x_i\ge r_i)$，要么是 $r_i-x_i\ (r_i\ge x_i)$，考虑到 $n\le 10$，我们可以 $2^n$ 暴力枚举每一项的正负号，即拆成 $\sum\limits_{i=0}^{n}(p_i\cdot x_i + q_i\cdot y_i)$ 其中 $p_i$ 和 $q_i$ 对应它们所取的正负号，那么显然 $\sum\limits_{i=0}^{n}p_i\cdot x_i$ 可以直接算出来。对于 $\sum\limits_{i=0}^{n}q_i\cdot r_i$ 的部分，不妨换个角度，它等价于 $\sum\limits_{i=0}^{m}v_i\cdot s_i$，也就是对每道题目单独记分，其中 $v_i$ 可以根据枚举的二进制计算每个同学所取正负号，而 $s_i$ 为该题的分值，问题转化为从 $[1,2,\cdots,m]$ 为每道题目分配一个分值 $s_i\ (1\le s_i\le m)$ 使得 $\sum\limits_{i=0}^{m}v_i\cdot s_i$ 最大化，因此可以直接贪心为 $v_i$ 更大的题目分配更高的分值即可！

此外，需要思考枚举过程中出现取 $x_i-r_i$ 但 $x_i\le r_i$ 的情况时怎么办，但我们发现在这种情况下无论如何它都不是全局的最大值，因为存在与它相反的状态（即取 $r_i-x_i$）却能比 $x_i-r_i$（负数）获得更高的答案 $r_i-x_i$（正数），因此直接做即可。

**代码：**
```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = (1 << 10) + 5;
const int maxm = 1e4 + 5;
int x[10], p[maxm], q[maxm], v[maxm], g[maxm];
char y[10][maxm];
int main() {
	int t, n, m;
	scanf("%d", &t);
	while (t--) {
		scanf("%d%d", &n, &m);
		for (int i = 0; i < n; i++) {
			scanf("%d", &x[i]);
		}
		for (int i = 0; i < n; i++) {
			scanf("%s", y[i]);
		}
		int up = (1 << n) - 1, mx = -1;
		for (int s = 0; s <= up; s++) {
			fill(v, v + m, 0);
			for (int i = 0; i < n; i++) {
				for (int j = 0; j < m; j++) {
					if (y[i][j] == '1') {
						v[j] += ((1 << i) & s) ? +1 : -1;
					}
				}
			}
			int res = 0;
			for (int i = 0; i < n; i++) {
				res += ((1 << i) & s) ? -x[i] : +x[i];
			}
			for (int i = 0; i < m; i++) {
				q[i] = i;
			}
			sort(q, q + m, [&](const int& a, const int& b) {
				return v[a] < v[b];
			});
			for (int i = 0; i < m; i++) {
				res += v[q[i]] * (i + 1);
			}
			if (res > mx) {
				mx = res;
				for (int i = 0; i < m; i++) p[q[i]] = i;
			}
		}
		for (int i = 0; i < m; i++) {
			printf("%d ", p[i] + 1);
		}
		puts("");
	}
	return 0;
}
```