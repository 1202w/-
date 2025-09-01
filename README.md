# 离线算法

离线算法是基于「求解前已知所有数据」这一假设来设计的，适用于有多组询问的题目。
离线算法的常见思路包括将询问统一求解（如 CDQ 分治）、通过一个询问的答案求出另外相似询问的答案（如 整体二分 和 莫队算法）等。
由于离线算法是一种思想而并不是某种具体的算法，因此它会搭配各种各样的数据结构或算法一起使用，与之相关的题目种类也更为繁杂。


## CDQ分治

前置知识点：分治（归并排序），树状数组

---

CDQ分治是一种强大的离线算法思想，由陈丹琦（cdq）在国家集训队论文中首次提出。其核心是将一个复杂的问题（通常是多维的、动态的）通过分治降维，分而治之。它最经典的应用是处理多维偏序问题，以及将一些动态问题转化为静态问题求解。

### 例题1：三维偏序

[P3810 【模板】三维偏序（陌上花开） - 洛谷](https://www.luogu.com.cn/problem/P3810)

##### 问题描述

有 n 个元素，第 i 个元素有 ai,bi,ci 三个属性。

定义一个函数 f(i)，它表示满足以下所有条件的元素 j 的数量：

- aj ≤ ai
- bj ≤ bi
- cj ≤ ci
- j != i

##### 求解目标

对于 d∈[0,n) 的每个整数值，分别求出满足 f(i)=d 的 i 的数量。

##### 分析

已知分治可以求逆序对，树状数组可以求逆序对，那么分治加树状数组呢？

可以求三维偏序。实际上就是两个求逆序对的方法结合。

乍一看感觉有点复杂，但我们可以通过降维将问题逐步简化。

假设我们现在有一组数据：

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/0481363834185f443c9280ef193ff4ca.png)

我们的大顺序按 **a** 属性的大小进行排序可以得到：

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/eef89c717d69da8f7dad17f98936a763.png)

我们以4，5为边界将其划分为左区和右区，

一个元素 `i` 的最终答案 `f(i)`，其贡献来源于三个部分：

1. **分治左区**内部元素对左区元素的贡献。
2. **分治右区**内部元素对右区元素的贡献。
3. **左区**元素对**右区**元素的贡献。

```cpp
void cdq(int l, int r)
{
    if (l == r)
        return;
    int mid = (l + r) >> 1;
    cdq(l, mid);      // 左边去跑递归，完成左区内部贡献
    cdq(mid + 1, r);  // 右边去跑递归，完成右区内部贡献
    merge(l, mid, r); // 左右都跑完再进行左跨右贡献
}
```

我们可以通过递归去完成左区和右区内部贡献，那么现在关键就在于左跨右贡献要如何统计。

**第一次降维**：由于大顺序是按照 **a** 属性的大小进行排序的，所以我们可以知道右区内的所有元素的 **a** 属性都大于等于左区所有元素的 **a** 属性。至此，我们已经可以忽略 **a** 属性的影响，该问题简化为二维。

我们将左右区元素分别按照 **b** 属性进行排序。

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/9fb6f7a21787f13f186a930eaca2541a.png)

接着我们来遍历右区的每一个元素，寻找左区有多少元素能够对其产生贡献。

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/5a5d2f5de8c1e0e1bffcfa2ff11755ef.png)

上面是树状数组的初始状态。现在 **a** 属性和 **b** 属性都处于有序状态了，我们用树状数组来记录 **c** 属性。

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/d3c04fdbbea3faf66fb9bd4c5bd14d0b.png)

我们从左区寻找左区内 **b** 属性小于等于右区当前元素 **b** 属性的，可见左区内只有1号符合该条件，所以左区的指针移动到1号（初始为0号），注意，该挪动是不回退的，**b** 属性已经做好排序，随着右边元素遍历， 左区指针只会往后走。左区指针每次挪动时，我们用树状数组记录指针到达元素的 **c** 属性，此时树状数组状态应当如下。

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/0d03a3a74df1df644461b8e690e2fc5a.png)

**第二次降维**：由于左区指针所达位置及之前元素的 **b** 属性都小于等于右区当前元素，目前我们已经不用考虑 **b** 属性影响，该问题简化为一维。我们现在只需要考虑 **c** 属性。

统计左侧多少元素可以对右侧当前元素结果产生贡献，现在只用检查左区有多少元素 **c** 属性满足，那我们直接利用树状数组即可，右侧一号元素所加贡献为`sum(4)`（4为该元素的 **c** 属性值。我们继续遍历右侧元素。

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/85055742d590746ab1ecc761f5948645.png)

遍历到右侧2号元素时，左侧指针可以挪动到3号，挪动过程中记录 **c** 属性值，目前树状数组的状态应当为

![img](https://kkkey11-image.oss-cn-shenzhen.aliyuncs.com/blog/5b1605f24523fc7cd5a06af36cd3fd77.png)

那么右侧2号元素所得到的贡献为`sum(4)`,后面的遍历也是如此。遍历完右侧元素之后，我们已经完成了cdq分治中最重要的左跨右贡献。

这道题目还有一个小细节，如果有几个abc属性都相同的元素，他们应当是互相符合要求的，但是按照 **a** 属性排序以后，前面的元素是得不到后面元素的贡献的，所以我们需要进行一个预处理，提前将相同的元素加上后面应当有但是在分治中得不到的贡献。

##### 代码实现

```cpp
#include <algorithm>
#include <iostream>
using namespace std;
const int N = 1e5 + 1;
int c[N];   // 树状数组
int f[N];   // 记录每个元素所得到的贡献
int ans[N]; // 储存答案
int n, k;   // n为元素个数，k为属性最大值
// 以下为树状数组函数
int lowbit(int x) { return x & -x; }
void add(int x, int y)
{
    for (int i = x; i <= k; i += lowbit(i))
        c[i] += y;
}
int sum(int x)
{
    int res = 0;
    for (int i = x; i; i -= lowbit(i))
        res += c[i];
    return res;
}
// 我们用结构体来储存每个元素信息
struct aa
{
    int i, a, b, c;
    bool operator==(const aa &k) const
    {
        return (a == k.a && b == k.b && c == k.c);
    }
} a[N];
// 以a为第一关键字排序，若相同再按b，c进行排序，使得所有属性相同元素可以放在一起，便于提前加贡献
bool coma(const aa &a, const aa &b)
{
    if (a.a != b.a)
        return a.a < b.a;
    if (a.b != b.b)
        return a.b < b.b;
    return a.c < b.c;
}
// 按b排序
bool comb(const aa &a, const aa &b)
{
    return a.b < b.b;
}
// 左跨右贡献
void merge(int l, int mid, int r)
{
    //我们在上一层递归中已经将左右两个部分按照b属性排好序了，也就是该函数最后一行所做的事情
    int p = l - 1;                     // p为左区指针
    for (int i = mid + 1; i <= r; i++) // 遍历右侧元素
    {
        // 左区符合条件则挪动指针
        while (p + 1 <= mid && a[p + 1].b <= a[i].b)
        {
            p++;
            add(a[p].c, 1); // 树状数组记录c属性信息
        }
        f[a[i].i] += sum(a[i].c); // 右区当前元素得到贡献
    }
    // 注意，遍历完之后我们需要清空树状数组，撤销之前所记录的信息，避免其对其他分支产生影响
    for (int i = l; i <= p; i++)
        add(a[i].c, -1);
    sort(a + l, a + r + 1, comb); // 将该部分全部按b进行排序
}
// cdq分治主函数
void cdq(int l, int r)
{
    if (l == r)
        return;
    int mid = (l + r) >> 1;
    cdq(l, mid);      // 左边去跑cdq分治
    cdq(mid + 1, r);  // 右边去跑cdq分治
    merge(l, mid, r); // 左右都跑完再进行左跨右贡献
}
int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> a[i].a >> a[i].b >> a[i].c;
    sort(a + 1, a + n + 1, coma);
    // 以下为预贡献操作
    int cnt = 1;
    a[1].i = 1;
    for (int i = 2; i <= n; i++)
    {
        a[i].i = i;
        if (a[i] == a[i - 1])
            cnt++;
        else
        {
            for (int j = i - cnt; j < i; j++)
                f[j] += i - j - 1; // 加上后面所有相同元素的个数
            cnt = 1;
        }
    }
    for (int j = n - cnt + 1; j < n + 1; j++)
        f[j] += n - j;
    // 开始跑cdq分治，然后统计答案，输出答案。
    cdq(1, n);
    for (int i = 1; i <= n; i++)
        ans[f[i]]++;
    for (int i = 0; i < n; i++)
        cout << ans[i] << "\n";
}
```

---

​	以上便是cdq分治的核心思想，面对一个多维问题时，应当如何逐步降维解决问题。但常常我们难以发现这是三维问题，下面我们具体问题具体分析。

#### eg1：动态逆序对

[P3157 [CQOI2011\] 动态逆序对 - 洛谷](https://www.luogu.com.cn/problem/P3157)

**问题简述**：现在给出 1∼*n* 的一个排列，按照某种顺序依次删除 *m* 个元素，你的任务是在每次删除一个元素**之前**统计整个序列的逆序对数。

**cdq分治的切入点：** cdq分治的核心思想是**将动态问题转化为静态问题**。对于一个包含时间和修改操作的序列，我们可以把每个操作都看作一个**事件**，然后按时间顺序来处理。cdq分治通过对时间进行分治，可以巧妙地处理“一个修改对未来所有查询的影响”。

具体到这道题，我们可以把问题分解为两个部分：

1. **初始状态下的逆序对数**：这是个静态问题，可以用归并排序或树状数组轻松解决。
2. **每次修改带来的逆序对数的增量**：这是动态部分，也是cdq分治要解决的核心。总的逆序对数就是初始逆序对数加上所有增量的累加和。

所以，我们的目标就是计算出**每一次操作事件所引起的逆序对数量的变化量**。

那么每个增加/删除事件应当有4个属性：时间，操作属性（增加/删除），增加/删除的值，值的原始序列。

请自行思考该如何转化为三维问题：**大顺序**应当取什么，**分治内顺序**应当如何，树状数组记录的数据应当是什么。

#### eg2：序列

[P4093 [HEOI2016/TJOI2016\] 序列 - 洛谷](https://www.luogu.com.cn/problem/P4093)

**问题简述：**给定一个长度为n的数列，以及m个可能的单点修改操作。要求你从原数列中选择一个最长的子序列，使得**无论**这m个修改中的任意一个是否发生，这个子序列都是不降的。输出这个最长不降子序列的长度。

**注意**：修改只会取其中任意一个或者不发生。

**分析：**

这很明显是dp问题，对于每一个单点，应当有三个值：原始值`v`，所能到达的最大值`vr`，所能到达的最小值`vl`。

假设我们有两个点 **ai** 和 **aj** ，在什么情况下，`dp[i]`可以等于`dp[j]+1`？

1. `j<i`;
2. 如果当前点发生了修改，那么它不管怎么改，都应当是大于等于`v[j]`的，也就是`vl[i]>=v[j]`;
3. 如果`j`点发生了修改，那么它不管怎么改，都应当是小于等于`v[i]`的，也就是`vr[j]<=v[i]`;

对于每一个`i`,我们都想找到**最大的**符合条件的`dp[j]`,我们可以对**树状数组的功能**进行修改，将其功能变为求前缀最大值，具体如何修改请自行学习树状数组。

该问题的关键已经讲完。

请自行思考该如何转化为三维问题：**大顺序**应当取什么，**分治内顺序**应当如何，树状数组记录的数据应当是什么。

注：分治内顺序左右组的参照值可不同。

## 整体二分

**引入**
在信息学竞赛中，有一部分题目可以使用二分的办法来解决。但是当这种题目有多次询问且我们每次查询都直接二分可能导致 TLE 时，就会用到整体二分。整体二分的主体思路就是把多个查询一起解决。（所以这是一个离线算法）



### 例题1：

区间内第k小
给定一个长度为n的数组，接下来有m条查询，格式如下
查询 l r k : 打印[l..r]范围内第k小的值
1 <= n、m <= 2 * 10^5
1 <= 数组中的数字 <= 10^9

测试链接 : https://www.luogu.com.cn/problem/P3834

##### 分析
首先考虑只有一条查询时的做法
我们可以考虑二分数组中出现过的值，可以用结构体存数组下标和值，把值从小到大排序，每次二分的下标mid,把小于等于mid的下标结构体数组中对应下标看在l到r中的个数是否大于等于k进行check
这样复杂度为 n*logn
但当查询数过多时复杂度为 n * m * logn
所以我们要考虑优化，考虑能否在二分时多组查询一起考虑
定义这样一个函数

```
void compute(int ql, int qr, int vl, int vr)
//当前任务区间为ql到qr，问题答案要二分区间为vl到vr
```

先二分中点mid=(pl+pr)/2,把vl到mid对应结构体数组中下标在树状数组中加上，再把ql到qr问题中分为两部分左部分为在相应查询区间中出现下标数量大于等于k，右部分小于k，右边问题的k还要减去已经出现数量，一直这样递归下去，最终当vl==vr时对应问题区间答案就就是当前下标vl结构体数组的值
时间复杂度为（n+m）* logn * logn

##### 代码实现
```
#include <bits/stdc++.h>

using namespace std;

struct Number {
    int i, v;
};

bool NumberCmp(Number x, Number y) {
    return x.v < y.v;
}

const int MAXN = 200001;
int n, m;

Number arr[MAXN];

int qid[MAXN];//当前下标问题的id
int l[MAXN];
int r[MAXN];
int k[MAXN];

int tree[MAXN];

int lset[MAXN];
int rset[MAXN];

int ans[MAXN];
//树状数组实现
int lowbit(int i) {
    return i & -i;
}

void add(int i, int v) {
    while (i <= n) {
        tree[i] += v;
        i += lowbit(i);
    }
}

int sum(int i) {
    int ret = 0;
    while (i > 0) {
        ret += tree[i];
        i -= lowbit(i);
    }
    return ret;
}

int query(int l, int r) {
    return sum(r) - sum(l - 1);
}

void compute(int ql, int qr, int vl, int vr) {
    //当前任务区间为ql到qr，问题答案要二分区间为vl到vr
    if (ql > qr) {
        return;
    }
    if (vl == vr) {//结算任务区间答案
        for (int i = ql; i <= qr; i++) {
            ans[qid[i]] = arr[vl].v;
        }
    } else {
        int mid = (vl + vr) >> 1;
        for (int i = vl; i <= mid; i++) {
            //对应下标加1
            add(arr[i].i, 1);
        }
        int lsiz = 0, rsiz = 0;
        for (int i = ql; i <= qr; i++) {
            int id = qid[i];
            int satisfy = query(l[id], r[id]);//当前任务区间内包含下标数量
            
            if (satisfy >= k[id]) {
                lset[++lsiz] = id;
            } else {
                如果左边不够，则右边要减去已经出现数量，去右边找
                k[id] -= satisfy;
                rset[++rsiz] = id;
            }
        }
        for (int i = 1; i <= lsiz; i++) {
            qid[ql + i - 1] = lset[i];
        }
        for (int i = 1; i <= rsiz; i++) {
            qid[ql + lsiz + i - 1] = rset[i];
        }
        for (int i = vl; i <= mid; i++) {
            //把对树状数组影响去掉
            add(arr[i].i, -1);
        }
        compute(ql, ql + lsiz - 1, vl, mid);
        compute(ql + lsiz, qr, mid + 1, vr);
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        arr[i].i = i;
        cin >> arr[i].v;
    }
    for (int i = 1; i <= m; i++) {
        qid[i] = i;
        cin >> l[i] >> r[i] >> k[i];
    }
    sort(arr + 1, arr + n + 1, NumberCmp);
    compute(1, m, 1, n);
    for (int i = 1; i <= m; i++) {
        cout << ans[i] << '\n';
    }
    return 0;
}
```

#### 整体二分的应用场景
1，单个问题的答案具有单调性，当答案固定时，问题的要求可能得到满足或者无法满足
2，如果要求得到满足，那么答案变差，要求依然能满足，此时去寻找更好的答案
3，如果要求无法满足，那么答案变好，要求也无法满足，此时去寻找更差的答案
4，问题数量较多，无法对每个问题都执行一次二分答案，但是所有查询允许离线处理

#### 整体二分的基本思想
1，让修改操作适配当前的答案范围，调整好数据状况，然后查看每个问题的要求是否达标
2，多个问题共享这一轮的修改操作后的数据状况，避免重复劳动
3，达标的问题去往左组，不达标的问题去往右组，然后左右分别递归，直到解决所有的问题

### 例题2：

 矩阵内第k小
 给定一个n * n的矩阵，接下来有q条查询，格式如下
 查询 a b c d k : 左上角(a, b)，右下角(c, d)，打印该区域中第k小的数
 1 <= n <= 500
 1 <= q <= 6 * 10^4
 0 <= 矩阵中的数字 <= 10^9
 测试链接 : https://www.luogu.com.cn/problem/P1527

#### 分析：

大体上于上一题一样，只不过查询和加入时用的是二维树状数组，用于快速查询二维区间内点数

### 代码实现

```

#include <bits/stdc++.h>

using namespace std;

struct Number {
    int x, y, v;
};

bool NumberCmp(Number a, Number b) {
    return a.v < b.v;
}

const int MAXN = 501;
const int MAXQ = 1000001;
int n, q;

Number xyv[MAXN * MAXN];
int cntv = 0;

int qid[MAXQ];
int a[MAXQ];
int b[MAXQ];
int c[MAXQ];
int d[MAXQ];
int k[MAXQ];

int tree[MAXN][MAXN];

int lset[MAXQ];
int rset[MAXQ];

int ans[MAXQ];

int lowbit(int i) {
    return i & -i;
}

void add(int x, int y, int v) {
    for (int i = x; i <= n; i += lowbit(i)) {
        for (int j = y; j <= n; j += lowbit(j)) {
            tree[i][j] += v;
        }
    }
}

int sum(int x, int y) {
    int ret = 0;
    for (int i = x; i > 0; i -= lowbit(i)) {
        for (int j = y; j > 0; j -= lowbit(j)) {
            ret += tree[i][j];
        }
    }
    return ret;
}

int query(int a, int b, int c, int d) {
    return sum(c, d) - sum(a - 1, d) - sum(c, b - 1) + sum(a - 1, b - 1);
}

void compute(int ql, int qr, int vl, int vr) {
    if (ql > qr) {
        return;
    }
    if (vl == vr) {
        for (int i = ql; i <= qr; i++) {
            ans[qid[i]] = xyv[vl].v;
        }
    } else {
        int mid = (vl + vr) >> 1;
        for (int i = vl; i <= mid; i++) {
            add(xyv[i].x, xyv[i].y, 1);
        }
        int lsiz = 0, rsiz = 0;
        for (int i = ql; i <= qr; i++) {
            int id = qid[i];
            int satisfy = query(a[id], b[id], c[id], d[id]);
            if (satisfy >= k[id]) {
                lset[++lsiz] = id;
            } else {
                k[id] -= satisfy;
                rset[++rsiz] = id;
            }
        }
        for (int i = 1; i <= lsiz; i++) {
            qid[ql + i - 1] = lset[i];
        }
        for (int i = 1; i <= rsiz; i++) {
            qid[ql + lsiz + i - 1] = rset[i];
        }
        for (int i = vl; i <= mid; i++) {
            add(xyv[i].x, xyv[i].y, -1);
        }
        compute(ql, ql + lsiz - 1, vl, mid);
        compute(ql + lsiz, qr, mid + 1, vr);
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> q;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            xyv[++cntv].x = i;
            xyv[cntv].y = j;
            cin >> xyv[cntv].v;
        }
    }
    for (int i = 1; i <= q; i++) {
        qid[i] = i;
        cin >> a[i] >> b[i] >> c[i] >> d[i] >> k[i];
    }
    sort(xyv + 1, xyv + cntv + 1, NumberCmp);
    compute(1, q, 1, cntv);
    for (int i = 1; i <= q; i++) {
        cout << ans[i] << '\n';
    }
    return 0;
}

```

## 莫队算法
#### 莫队算法简介
莫队算法是由莫涛提出的算法。在莫涛提出莫队算法之前，莫队算法已经在 Codeforces 的高手圈里小范围流传，但是莫涛是第一个对莫队算法进行详细归纳总结的人。莫涛提出莫队算法时，只分析了普通莫队算法，但是经过 OIer 和 ACMer 的集体智慧改造，莫队有了多种扩展版本。

莫队算法可以解决一类离线区间询问问题，适用性极为广泛。同时将其加以扩展，便能轻松处理树上路径询问以及支持修改操作。


#### 形式
假设 n=m，那么对于序列上的区间询问问题，如果从 
[l,r] 的答案能够 O(1) 扩展到 [l-1,r],[l+1,r],[l,r+1],[l,r-1]（即与 [l,r] 相邻的区间）的答案，那么可以在 n*sqrt(n) 的复杂度内求出所有询问的答案。


#### 解释
离线后排序，顺序处理每个询问，暴力从上一个区间的答案转移到下一个区间答案（一步一步移动即可）。

#### 排序方法
对于区间 [l,r], 以 l 所在块的编号为第一关键字，r 为第二关键字从小到大排序。
### 普通莫队
### 例题1：

普通莫队模板题
给定一个长度为n的数组arr，一共有q条查询，格式如下
查询 l r : 打印arr[l..r]范围上有几种不同的数字
1 <= n <= 3 * 10^4
1 <= arr[i] <= 10^6
1 <= q <= 2 * 10^5

#### 分析

按上述方法分块，用数组记录每种数字出现多少次，和用变量记录当前区间不同数字个数

#### 代码实现
```

#include <bits/stdc++.h>

using namespace std;

struct Query {
    int l, r, id;
};

const int MAXN = 30001;
const int MAXV = 1000001;
const int MAXQ = 200001;
int n, q;
int arr[MAXN];
Query query[MAXQ];

int bi[MAXN];
int cnt[MAXV];
int kind = 0;

int ans[MAXQ];

bool QueryCmp1(Query &a, Query &b) {
    if (bi[a.l] != bi[b.l]) {
        return bi[a.l] < bi[b.l];
    }
    return a.r < b.r;
}

bool QueryCmp2(Query &a, Query &b) {
    if (bi[a.l] != bi[b.l]) {
        return bi[a.l] < bi[b.l];
    }
    if ((bi[a.l] & 1) == 1) {
        return a.r < b.r;
    } else {
        return a.r > b.r;
    }
}

void del(int num) {
    if (--cnt[num] == 0) {
        kind--;
    }
}

void add(int num) {
    if (++cnt[num] == 1) {
        kind++;
    }
}

void prepare() {
    int blen = (int)sqrt(n);
    for (int i = 1; i <= n; i++) {
        bi[i] = (i - 1) / blen + 1;
    }
    sort(query + 1, query + q + 1, QueryCmp2);
}

void compute() {
    int winl = 1, winr = 0;
    for (int i = 1; i <= q; i++) {
        int jobl = query[i].l;
        int jobr = query[i].r;
        while (winl > jobl) {
            add(arr[--winl]);
        }
        while (winr < jobr) {
            add(arr[++winr]);
        }
        while (winl < jobl) {
            del(arr[winl++]);
        }
        while (winr > jobr) {
            del(arr[winr--]);
        }
        ans[query[i].id] = kind;
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> arr[i];
    }
    cin >> q;
    for (int i = 1; i <= q; i++) {
        cin >> query[i].l;
        cin >> query[i].r;
        query[i].id = i;
    }
    prepare();
    compute();
    for (int i = 1; i <= q; i++) {
        cout << ans[i] << '\n';
    }
    return 0;
}
```
#### 常数优化
当块号为奇数时按问题右边界从小到大排序
当块号为偶数时按问题右边界从大到小排序
代码如下
```
bool QueryCmp2(Query &a, Query &b) {
    if (bi[a.l] != bi[b.l]) {
        return bi[a.l] < bi[b.l];
    }
    if ((bi[a.l] & 1) == 1) {
        return a.r < b.r;
    } else {
        return a.r > b.r;
    }
}
```
### 带修莫队

#### 例题2：

给定一个长度为n的数组arr，一共有m条操作，操作格式如下
操作 Q l r     : 打印arr[l..r]范围上有几种不同的数 
操作 R pos val : 把arr[pos]的值设置成val
1 <= n、m <= 2 * 10^5
1 <= arr[i]、val <= 10^6
测试链接 : https://www.luogu.com.cn/problem/P1903

#### 分析
带修莫队的原理
1，每条修改操作，都分配修改时间点，那么查询任务可以由[jobl, jobr, jobt]描述
2，下标窗口[winl..winr]和普通莫队设定相同
3，时间窗口[..wint]，代表数组的状况，已经更新到了，wint这个修改时间点
4，下标窗口 + 时间窗口，一起决定窗口的信息，信息可以快速更新正确
5，序列分块的块长设置为 $n^{2/3}$
6，对查询任务进行排序，然后依次处理，执行总时间O($n^{5/3}$)，假设所有数据量同阶

任务的排序策略
1，jobl所在块的编号，从小到大排序
2，jobr所在块的编号，从小到大排序
3，jobt的数值，从小到大排序





#### 代码实现

```

#include <bits/stdc++.h>

using namespace std;

struct Query {
    int l, r, t, id;
};

struct Update {
    int pos, val;
};

const int MAXN = 200001;
const int MAXV = 1000001;
int n, m;
int arr[MAXN];
int bi[MAXN];

Query query[MAXN];
Update update[MAXN];
int cntq, cntu;

int cnt[MAXV];
int kind;

int ans[MAXN];

bool QueryCmp(Query &a, Query &b) {
    if (bi[a.l] != bi[b.l]) {
        return bi[a.l] < bi[b.l];
    }
    if (bi[a.r] != bi[b.r]) {
        return bi[a.r] < bi[b.r];
    }
    return a.t < b.t;
}

void del(int num) {
    if (--cnt[num] == 0) {
        kind--;
    }
}

void add(int num) {
    if (++cnt[num] == 1) {
        kind++;
    }
}

void moveTime(int jobl, int jobr, int tim) {
    int pos = update[tim].pos;
    int val = update[tim].val;
    if (jobl <= pos && pos <= jobr) {
        del(arr[pos]);
        add(val);
    }
    int tmp = arr[pos];
    arr[pos] = val;
    update[tim].val = tmp;
}

void compute() {
    int winl = 1, winr = 0, wint = 0;
    for (int i = 1; i <= cntq; i++) {
        int jobl = query[i].l;
        int jobr = query[i].r;
        int jobt = query[i].t;
        int id = query[i].id;
        while (winl > jobl) {
            add(arr[--winl]);
        }
        while (winr < jobr) {
            add(arr[++winr]);
        }
        while (winl < jobl) {
            del(arr[winl++]);
        }
        while (winr > jobr) {
            del(arr[winr--]);
        }
        while (wint < jobt) {
            moveTime(jobl, jobr, ++wint);
        }
        while (wint > jobt) {
            moveTime(jobl, jobr, wint--);
        }
        ans[id] = kind;
     }
}

void prepare() {
    int blen = max(1, (int)pow(n, 2.0 / 3));
    for (int i = 1; i <= n; i++) {
        bi[i] = (i - 1) / blen + 1;
    }
    sort(query + 1, query + cntq + 1, QueryCmp);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> arr[i];
    }
    char op;
    int l, r, pos, val;
    for (int i = 1; i <= m; i++) {
        cin >> op;
        if (op == 'Q') {
            cin >> l >> r;
            cntq++;
            query[cntq].l = l;
            query[cntq].r = r;
            query[cntq].t = cntu;
            query[cntq].id = cntq;
        } else {
            cin >> pos >> val;
            cntu++;
            update[cntu].pos = pos;
            update[cntu].val = val;
        }
    }
    prepare();
    compute();
    for (int i = 1; i <= cntq; i++) {
        cout << ans[i] << '\n';
    }
    return 0;
}

```
