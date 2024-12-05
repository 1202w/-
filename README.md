### 1.P1087 FBI树
用tree[i]表示节点的类型，调用递归在s的下标1到2^n,如果当前为叶子节点则直接判断输出否则每次继续调用左子树和右子树最后由左孩子和右孩子整合出该节点类型 ,最后调用输出函数后序遍历输出。
```
#include<iostream>
#include<algorithm> 
using namespace std;
int n;
string s;
char tree[5009];
void build(int p, int pl, int pr) {
    //当前节点为p,包括的范围为pl到pr
    if (pl == pr) {//如果当前为叶子节点直接判断当前节点类型并返回
        if (s[pl] == '0') {
            tree[p] = 'B';
        }
        else {
            tree[p] = 'I';
        }
        return;
    }
    int mid = (pl + pr) / 2;
    //分别调用左孩子和右孩子
    //左孩子为2*p,右孩子为2*p+1
    build(2 * p, pl, mid);
    build(2 * p + 1, mid + 1, pr);
    //由左孩子和右孩子信息判断出当前节点类型
    if (tree[2 * p] == 'B' && tree[2 * p + 1] == 'B') {
        tree[p] = 'B';
    }
    else if (tree[2 * p] == 'I' && tree[2 * p + 1] == 'I') {
        tree[p] = 'I';
    }
    else {
        tree[p] = 'F';
    }
}
void print(int p,int pl,int pr) {
    
    int mid = (pl + pr) / 2;
    if (pl == pr) {//如果当前为叶子节点直接输出并返回
        cout << tree[p];
        return;
    }
    //后续遍历输出
    print(2 * p, pl, mid);
    print(2 * p + 1, mid + 1, pr);
    cout << tree[p];
}
int main()
{

    cin >> n;
    cin >> s;
    s = " " + s;
    build(1, 1, 1 << n);
    print(1, 1, 1 << n);
    return 0;
}

```
### 2.P1030 求先序排列
对应的中序和后序排列中的根为后序最后一个，因为所有的值都不一样，可以暴力在中序中找出这个值对应下标，就可以递归左孩子和右孩子，按先序遍历输出。
```
#include<iostream>
#include<algorithm> 
using namespace std;

string s1,s2;
void print(string ts1, string ts2) {//中序和后序
    if (ts1.size() == 0)return;如果为空返回
    cout << ts2[ts2.size() - 1];
    int pos;//暴力在中序中寻找对应下标
    for (int i = 0; i < ts1.size(); i++) {
        if (ts1[i] == ts2[ts2.size() - 1]) {
            pos = i;
            break;
        }
    }
    //分别递归左孩子和右孩子
    print(ts1.substr(0, pos), ts2.substr(0, pos));
    print(ts1.substr(pos + 1), ts2.substr(pos, ts2.size() - pos - 1));
}
int main()
{
    cin >> s1>>s2;
    print(s1, s2);
    return 0;
}

```

### 3.P1305 新二叉树
按题意构造出二叉树，然后先序遍历输出。
```
#include <iostream>
#include<algorithm>
#include<string>
using namespace std;
int n;
struct node {
    int l, r;
}a[29];
string s;
void print(int now) {
    if (now == 0) {//当前为空返回
        return;
    }
    printf("%c", now + 'a' - 1);//输出当前值
    print(a[now].l);//左孩子
    print(a[now].r);//右孩子
}
int main()
{
    cin >> n;
    int root;
    cin >> s;
    root = s[0] - 'a'+1;//用0表示空，1表示a，2表示b...
    a[root].l = s[1] == '*' ? 0 : s[1] - 'a'+1;
    a[root].r = s[2] == '*' ? 0 : s[2] - 'a'+1;
    for (int i = 2; i <= n; i++) {
        cin >> s;
      
        char f, l, r;
        f = s[0];
        l = s[1];
        r = s[2];
       
        if (l == '*') {
            l = 0;
        }
        else {
            l = l - 'a' + 1;
        }
        if (r == '*') {
            r = 0;
        }
        else {
            r = r - 'a' + 1;
        }
        f = f - 'a' + 1;
        a[f].l = l;
        a[f].r = r;
    }
    print(root);
    return 0;
}

```


### 4.P1229 遍历问题
只有一个儿子 的节点 才会在知道 前序后序 的情况下有不同的中序遍历，所以将题目转化成找 只有一个儿子的节点个数。
可以很容易的找出这类节点在前序后序中出现的规律。（前序中出现AB，后序中出现BA，则这个节点只有一个儿子）
每个这类节点有两种中序遍历（及儿子在左，儿子在右）根据乘法原理中序遍历数为 2^节点个数 种
```
#include<iostream>
#include<algorithm> 
using namespace std;

string s1,s2;

int main()
{
    cin >> s1>>s2;
   
    int ans = 0;
    for (int i = 0; i < s1.size()-1; i++) {
        for (int j = 0; j < s2.size()-1; j++) {
            if (s1[i] == s2[j + 1] && s2[j] == s1[i + 1]) {//找到一组数满足条件
                ans++;
            }
        }
    }
    cout << (1 << ans);
    return 0;
}

```
### 5.P2168 荷马史诗 
 转换一下题意，不难知道我们需要维护的是最短的带权路径之和和该哈夫曼树的高度。然后便是如何维护，由于不需要知道哈夫曼树的具体形态，我们便可以按照哈夫曼树的构造方式，将当前最小的K个节点合并为1个父节点，直至只有一个父节点。看到“将最小K个节点合并”便可以明确使用优先队列（二叉堆）进行维护。
 我们需要注意一个细节。因为每次都是将k个节点合并为1个（减少k-1个），一共要将n个节点合并为1个，如果（n-1）%（k-1）！=0 则最后一次合并时不足k个。也就表明了最靠近根节点的位置反而没有被排满，因此我们需要加入k-1-（n-1）%（k-1）个空节点使每次合并都够k个节点（也就是利用空节点将其余的节点挤到更优的位置上）。
```
#include <iostream>
#include<algorithm>
#include<string>
#include<queue>
using namespace std;
int n, k;
struct node {
    long long  w, h;
    
};
struct cmp {
    bool operator()(node a, node b) {
        if (a.w == b.w)return a.h > b.h;//当权值相同时高度小的排前面满足第二个答案的要求
        return a.w > b.w;
    }
};
int main()
{
    cin >> n >> k;
    priority_queue<node, vector<node>, cmp>q;
    for (int i = 1; i <= n; i++) {
        long long  w;
        cin >> w;
        q.push({ w,0 });
    }
    int cnt = 0;//判断要加几个空节点
    if ((n - 1) % (k - 1) != 0)cnt = k - 1 - (n - 1) % (k - 1);
    for (int i = 1; i <= cnt; i++) {
        q.push({ 0,0 });

    }
    
    long long ans = 0;
    while (q.size()>1) {//当q中元素个数大于一
       long long  sum = 0, maxh = 0;//分别是k个最小总和和k个中最大高度
        for (int i = 1; i <= k; i++) {
            sum += q.top().w;
            maxh = max(maxh, q.top().h);
            q.pop();
        }
        ans += sum;
        q.push({ sum,maxh + 1 });//新节点高度为k个节点最大高度+1

    }
    cout << ans << endl << q.top().h;
    return 0;
}
```
### 6.P4715 淘汰赛
由题易知亚军只有可能在左半边最大值和右半边最大值中，遍历判断即可
```
#include<iostream>
#include<algorithm> 
using namespace std;
int n;
int  mx1 = -1e9, mx2 = -1e9, id1, id2;//初始化最大值最小以便后续查找

int main()
{
   
    cin >> n;
    for (int i = 1; i <= (1 << (n - 1)); i++) {//左半边
        int x;
        cin >> x;
        
        if (x > mx1) {
            mx1 = x;
            id1 = i;
        }
    }
    for (int i = 1; i <= (1 << (n - 1)); i++) {//右半边
        int x;
        cin >> x;
        if (x > mx2) {
            mx2 = x;
            id2 = i + (1 << (n - 1));
        }
    }
    if (mx1 > mx2) {
        printf("%d", id2);
    }
    else {
        printf("%d", id1);
    }
    return 0;
}

```
### 7.P4913 二叉树深度
构建二叉树遍历查询即可。
```
#include <iostream>
#include<algorithm>
#include<string>
#include<queue>
using namespace std;
int n;
struct node {
    int l, r;
}tree[1000009];
int maxh = 0;//最大深度
void f(int now, int h) {
    if (now == 0)return;//如果为空直接返回不参与更新深度
    maxh = max(maxh, h);
    f(tree[now].l,h+1);//左孩子
    f(tree[now].r,h+1);//右孩子
}
int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++) {
        int l, r;
        cin >> l >> r;
        tree[i].l = l;
        tree[i].r = r;

    }
    f(1, 1);
    cout << maxh;
    return 0;
}

```
### 8.P1827 美国血统
与第二题类似解法。
```
#include<iostream>
#include<algorithm> 
#include<string>

using namespace std;
string s1, s2;
void dfs(string st1, string st2) {
    if (st1.size() == 0) {
        return;
    }
    char c = st2[0];
    int pos = st1.find(c);
    dfs(st1.substr(0, pos), st2.substr(1, pos));
    dfs(st1.substr(pos + 1), st2.substr(pos + 1));
    cout << c;

}
int main()
{
   
    cin >> s1 >> s2;
    dfs(s1, s2);

    return 0;
}


```
