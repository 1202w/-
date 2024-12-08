# 二叉树与哈夫曼树
### 一. 二叉树
1. **二叉树的性质**
   - 二叉树的每个节点最多有两个子节点，分别为左孩子和右孩子，以它们为根的子树被称为左子树和右子树
   - 二叉树的每层节点数以2的倍数递增，所以二叉树第i层最多有2^(i-1) 个节点。如果每层节点数都是满的，称它为满二叉树。一个n层的满二叉树，一共有2^n-1 个节点。如果满二叉树只在最后一层有缺失，并且缺失的编号都在最后，则称为完全二叉树。如下图所示是满二叉树和完全二叉树。
   [![pAIZprV.png](https://s21.ax1x.com/2024/12/01/pAIZprV.png)](https://imgse.com/i/pAIZprV)

   [![pAIZtMt.png](https://s21.ax1x.com/2024/12/01/pAIZtMt.png)](https://imgse.com/i/pAIZtMt)
   - 1号节点是二叉树的根，他是唯一没有父亲节点的节点。从根节点到节点u的路径长度定义为u的深度，节点u到它的叶子节点的最大路径长度定义为节点u的高度。根的高度最大，称为树的高。
2. **二叉树的存储结构**
   二叉树的一个节点的存储，包括节点的值，左右子节点，有动态和静态两种存储结构。
   - 动态二叉树:
     ```
     struct node{
        int value;    //节点的值
        node *l,*r    //指向左右子节点
     };
     ```
   - 静态数组存储二叉树。在算法竞赛中，为了编码简单，加快速度，一般用静态数组实现二叉树。一般0节点表示空节点，如果一个节点没有子节点那么l=r=0.下面定义一个大小为N的结构体数组。
     ```
     struct node{
        int value;    //节点的值
        int l,r;      //左右孩子
     }tree[N];
     ```
     特别地，用数组实现完全二叉树，访问非常便利。一颗数量为k=12的完全二叉树，比如:
     [![pAIZtMt.png](https://s21.ax1x.com/2024/12/01/pAIZtMt.png)](https://imgse.com/i/pAIZtMt)
     有以下性质：
     1. 编号i>1 的节点，其父亲节点编号为i/2(向下取整);
     2. 如果节点i有左右孩子，则左孩子节点为2i，右孩子是节点2i+1;
     3. 如果2i>k,那么节点i没有孩子;如果2i+1>k,那么节点i没有右孩子;
3. **二叉树的遍历**
   二叉树的遍历分为先序遍历,中序遍历,后序遍历。
   每种都是父节点按字面意思的访问顺序不同,但左孩子在右孩子之前。
   下面用之前提到的静态数组实现。
   [![pAIeI78.png](https://s21.ax1x.com/2024/12/01/pAIeI78.png)](https://imgse.com/i/pAIeI78)
   - 先序遍历,按父节点,左孩子,右孩子的顺序访问
     按上图输出为 1 2 4 5 8 3 6 9 10 7
     ```
     void print(int now){
        if(now==0)return;
        cout<<tree[now].value;
        print(tree[now].l);
        print(tree[now].r);
     }   
     ```
   - 中序遍历,按左孩子,父节点,右孩子的顺序访问
     按上图输出为 4 2 8 5 1 9 6 10 3 7
     ```
     void print(int now){
        if(now==0)return;
        print(tree[now].l);
        cout<<tree[now].value;
        print(tree[now].r);
     }   
     ```
   - 后序遍历,按左孩子,右孩子,父节点的顺序访问
     按上图输出为 4 8 5 2 9 10 6 7 3 1
     ```
     void print(int now){
        if(now==0)return;
        print(tree[now].l);
        print(tree[now].r);
        cout<<tree[now].value;
     }   
     ``` 
### 二. 哈夫曼树
- 树的路径长度是从根到每个节点的路径长度之和。
- 推广到带权节点。从根到一个带权节点的带权路径长度,是从根到该节点的路径长度与节点权值的乘积。树的带权路径长度是所有叶子节点的带权路径长度之和。比如下图:
  [![pAImL5D.png](https://s21.ax1x.com/2024/12/01/pAImL5D.png)](https://imgse.com/i/pAImL5D)
  叶子节点2带权路径长度为 3* 1=3;
  叶子节点4带权路径长度为 2* 2=4;
  叶子节点5带权路径长度为 2* 1=2;
  所以这棵树的带权路径长度为3+4+2=9
- 给定n个权值,构造一棵有n个叶子节点的二叉树,每个叶子节点对 应一个权值。有很多种构造方法,把其中带路径长度最小的二叉树称为哈夫曼树,或最优二叉树。
  如何构造一颗哈夫曼树? 容易想到一种贪心方法:把权值大的节点放在离根节点近的层次上,权值小的节点放在离根节点远的层次上。
- 先在n个点的集合中中选两个权值最小的点把这两个点的父亲的权值设为这两个点权值和,再把这个父亲放回集合中,依此类推重复这个过程直到集合中只剩下一个点为止，这个点为根节点。
  可以用优先队列加速寻找集合中最小两个点的操作。
### 题单

| 序号 | 题号        | 标题      | 题型   | 难度评级      | 
|------|------------|-----------|--------| -------------|
| 1    | luogu P1087| FBI树     | 二叉树    | ⭐⭐     |
| 2    | luogu P1030| 求先序排列 | 二叉树     | ⭐⭐    |
| 3    | luogu P1305| 新二叉树   | 二叉树   | ⭐⭐   |
| 4    | luogu P1229|  遍历问题   | 二叉树   | ⭐⭐⭐   |
| 5    | luogu P2168| 荷马史诗  | 二叉树/哈夫曼树   | ⭐⭐⭐⭐ |
| 6   | luogu P4715| 淘汰赛 | 二叉树   | ⭐⭐ |
| 7   | luogu P4913| 二叉树深度 | 二叉树   | ⭐⭐ |
| 8    | luogu P1827| 美国血统 | 二叉树   | ⭐⭐ |
| 9    | luogu P1122|最大子树和 | 二叉树 | ⭐⭐⭐ |
| 10   | luogu P1395|会议     | 二叉树 | ⭐⭐⭐ |
| 11    | luogu P2420| 让我们异或吧 | 二叉树 | ⭐⭐⭐ |
