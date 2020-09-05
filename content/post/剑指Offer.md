---
title: "剑指Offer所有编程题（C++代码实现）"
date: 2020-07-24T12:46:25+08:00
lastmod: 2020-07-24T12:46:25+08:00
draft: false
tags: [C++, 算法]
categories: [算法]
author: "Dereen"
comment: true
toc: true
autoCollapseToc: false
---

这里是所有剑指Offer编程题，我的C++代码实现，思想只是简要提及，用来做个备忘。

<!--more-->

## 二维数组中的查找

### 题目描述

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

### 思路及代码

假设arr数组，val，tar如下图所示：
如果我们把二分值定在右上角或者左下角，就可以进行二分。这里以右上角为例，左下角可自行分析：
![图片说明](https://uploadfiles.nowcoder.com/images/20200324/2071677_1585021381982_89033DB5EFA905C7F9FCCA6E59C9BB2C)
1）我么设初始值为右上角元素，arr[0]\[5] = val，目标tar = arr[3]\[1]
2）接下来进行二分操作：
3）如果val == target,直接返回
4）如果 tar > val, 说明target在更大的位置，val左边的元素显然都是 < val，**间接 < tar，说明第 0 行都是无效的，所以val下移到arr[1]\[5]**
5）如果 tar < val, 说明target在更小的位置，val下边的元素显然都是 > val，**间接 > tar，说明第 5 列都是无效的，所以val左移到arr[0]\[4]**
6）继续步骤2）

复杂度分析
时间复杂度：O(m+n) ，其中m为行数，n为列数，最坏情况下，需要遍历m+n次。
空间复杂度：O(1)

```cpp
bool Find(int target, vector<vector<int>> array)
{
    int row = array.size();             
    int col = array[0].size();
    if (!row || !col)
        return false;
    int i = row - 1, j = 0;
    while (i >= 0 && j < col)
    {
        if (array[i][j] > target)
            --i;
        else if (array[i][j] < target)
            ++j;
        else
            return true;
    }
    return false;
}
```

## 替换空格

### 题目描述

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

### 思路及代码

在原数组的基础上，先计算出原数组新的长度，然后再从后往前替换。

```cpp
void replaceSpace(char* str, int length)
{
	if (str == nullptr || length == 0)
		return;
	int newlen = 0;
	int numofSpace = 0;
	for (int i = 0; i < length; ++i)
	{
		if (str[i] == ' ')
			++numofSpace;
	}
	newlen = length + 2 * numofSpace;
	int oldIndex = length - 1;
	int newIndex = newlen - 1;
	while (oldIndex >= 0)
	{
		if (str[oldIndex] == ' ')
		{
			str[newIndex--] = '0';
			str[newIndex--] = '2';
			str[newIndex--] = '%';
		}
		else
			str[newIndex--] = str[oldIndex];
		--oldIndex;
	}
}
```

## 从头到尾打印链表

### 题目描述

输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

### 思路及代码

1. 正向插入，然后使用`std::reverse(array.begin(), array.end())`进行反转
2. 递归版本
3. 正向遍历，插入时，插入到array头部

```cpp
// 方案2代码

vector<int>array;
vector<int> printListFromTailToHead(ListNode* head) {
    if (head == NULL)
        return array;
    array = printListFromTailToHead(head->next);
    array.push_back(head->val);
    return array;

// 方案3 代码
vector<int> printListFromTailToHead(ListNode* head) {
    vector<int> array;
    if (head == NULL)
        return array;
    ListNode *curr = head;
    array.insert(array.begin(), head->val);
    while (curr->next != NULL)
    {
        curr = curr->next;
        array.insert(array.begin(), curr->val);
    }
    return array;
```

## 重建二叉树

### 题目描述

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

### 思路及代码

用递归的方法：

1. 由先序序列第一个**`pre[0]`**在中序序列中找到根节点位置**`gen`**

2. 以

   `gen`

   为中心遍历

   - `0~gen`

     左子树

     - 子中序序列：**`0~gen-1`**，放入**`vin_left[]`**
     - 子先序序列：**`1~gen`**放入**`pre_left[]`**，**`+1`**可以看图，因为头部有根节点

   - `gen+1~vinlen`

     为右子树

     - 子中序序列：**`gen+1 ~ vinlen-1`**放入**`vin_right[]`**
     - 子先序序列：**`gen+1 ~ vinlen-1`**放入**`pre_right[]`**

3. 由先序序列**`pre[0]`**创建根节点

4. 连接左子树，按照左子树子序列递归（**`pre_left[]`**和**`vin_left[]`**）

5. 连接右子树，按照右子树子序列递归（**`pre_right[]`**和**`vin_right[]`**）

6. 返回根节点

```cpp
struct TreeNode
{
	int val;
	TreeNode* left;
	TreeNode* right;

	TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

TreeNode* reConstructBinaryTree(vector<int> pre, vector<int> vin)
{
	int vinlen = vin.size();
	if (vinlen == 0)
		return nullptr;
	vector<int> pre_left, pre_right, vin_left, vin_right;
	// 创建根节点，根节点是先序遍历的第一个数
	TreeNode* head = new TreeNode(pre[0]);
	// 找到中序遍历根节点所在的位置，存放于变量gen中
	int gen = 0;
	for (int i = 0; i < vinlen; ++i)
		if (vin[i] == pre[0])
		{
			gen = i;
			break;
		}
	// 对于中序遍历，根节点左边的节点位于二叉树的左边，根节点右边的节点位于二叉树的右边
	// 左子树
	for (int i = 0; i < gen; ++i)
	{
		vin_left.push_back(vin[i]);
		pre_left.push_back(pre[i + 1]);
	}
	// 右子树
	for (int i = gen + 1; i < vinlen; ++i)
	{
		vin_right.push_back(vin[i]);
		pre_right.push_back(pre[i]);
	}
	head->left = reConstructBinaryTree(pre_left, vin_left);
	head->right = reConstructBinaryTree(pre_right, vin_right);
	return head;
}
```

## 用两个栈实现队列

### 题目描述

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

### 思路及代码

Push：直接进stack1；

Pop：如果stack2不为空，就直接出栈，如果stack2为空，则先把stack1中所有元素入stack2，然后stack2出栈。

```cpp
class Solution
{
public:
    void push(int node) {
        stack1.push(node);
    }

    int pop() {
        if (!stack2.empty())
        {
            int temp = stack2.top();
            stack2.pop();
            return temp;
        }
        else 
        {
            int temp2;
            while (!stack1.empty())
            {
                temp2 = stack1.top();
                stack1.pop();
                stack2.push(temp2);
            }
            stack2.pop();
            return temp2;
        }
        return 0;
    }

private:
    stack<int> stack1;
    stack<int> stack2;
};
```

## 旋转数组的最小数字

### 题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组[3,4,5,1,2]为[1,2,3,4,5]的一个旋转，该数组的最小值为1。NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

### 思路及代码

非递减数组，经过旋转后，有一个位置出现了递减，所以只要找出该位置即可，从前往后遇到递减输出后者，从前往后遇到递增，输出后者。

```cpp
int minNumberInRotateArray(vector<int> rotateArray) {
    int arrayLen = rotateArray.size();
    if (arrayLen == 0)
        return 0;
    if (arrayLen == 1)
        return rotateArray[0];
    for (int i =0, j = arrayLen - 1; i<j; ++i, --j)
    {
        if (rotateArray[i] > rotateArray[i + 1])
            return rotateArray[i + 1];
        if (rotateArray[j] < rotateArray[j - 1])
            return rotateArray[j];
    }
    return rotateArray[arrayLen - 1];
```

## 斐波那契数列

### 题目描述

大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）。

n<=39

### 思路及代码

1. 递归，最为简单，但性能也最差。

```cpp
int Fibonacci(int n) {
	if (n ==0 || n == 1)
        return n;
    return Fibonacci(n - 1) + Fibonacci(n - 2);
}
```

2. 记忆化搜索

拿求f[5] 举例
![ ](https://uploadfiles.nowcoder.com/images/20200331/284295_1585636370308_1946BA36B55EFC1A019C4017DE68D41D)

通过图会发现，方法一中，存在很多重复计算，因为为了改进，就把计算过的保存下来。
那么用什么保存呢？一般会想到map， 但是此处不用牛刀，此处用数组就好了。

```cpp
int Fib(int n, vector<int>& dp)
{
    if (n == 0 || n == 1)
        return n;
    if (dp[n] != -1)
        return dp[n];               // 这里很关键，即为如果已经求过f(5)则直接返回之前求过的结果
    return dp[n] = Fib(n - 1, dp) + Fib(n - 2, dp);
}

int Fibonacci(int n)
{
    vector<int> dp(n + 1, -1);
    return Fib(n, dp);
}
```

3. 动态规划

虽然方法二可以解决此题了，但是如果想让空间继续优化，那就用动态规划，优化掉递归栈空间。
方法二是从上往下递归的然后再从下往上回溯的，最后回溯的时候来合并子树从而求得答案。
那么动态规划不同的是，不用递归的过程，直接从子树求得答案。过程是从下往上。

```cpp
int Fibonacci(int n)
{
    if (n == 0 || n == 1)
        return n;
    int a = 0, b = 1, c;
	for (int i = 2; i <= n; ++i)
    {
        c = a + b;
        a = b;
        b = c;
    }
    return c;
}
```



## 跳台阶

### 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

### 思路及代码

考察将斐波那契数列应用的问题。题目分析，假设f[i]表示在第i个台阶上可能的方法数。逆向思维。如果我从第n个台阶进行下台阶，下一步有2中可能，一种走到第n-1个台阶，一种是走到第n-2个台阶。所以f[n] = f[n-1] + f[n-2].
那么初始条件了，f[0] = f[1] = 1。
所以就变成了：f[n] = f[n-1] + f[n-2], 初始值f[0]=1, f[1]=1，目标求f[n]。

这里和斐波那契数列不同的是，f[0]=1，这是因为，f[2]=2。

代码基本同上，这里只写出方法三的代码。

```cpp
int jumpFloor(int n) {
    if (n == 0 || n == 1)
        return n;
    int a = 1, b = 1, c;
    for (int i = 2; i <= n; ++i)
    {
        c = a + b;
        a = b;
        b = c;
    }
    return c;
}
```

## 变态跳台阶

### 题目描述

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

### 思路及代码

还是逆思维，从第n级台阶上跳一下可以跳到哪些台阶上呢？分别是0(地面)、1、2...n-1。于是，f[n]表示跳上第n级台阶的方法数。f[n]=f[n-1]+f[n-2]+...+f[2]+f[1]+f[0]。而f[2]=f[1]+f[0]=1+1=2。于是：代码如下

```cpp
int jumpFloorII(int number) {
    if (number == 0 || number == 1)
        return 1;
    vector<int> f(number+1, 0);
    f[0] = f[1] = 1;
    for (int i = 2; i <=number; ++i)
        for (int j = 0; j<i; j++)
            f[i] += f[j];
    return f[number];
}
```

观察后发现，答案是如下的列表：

{1, 1, 2, 4, 8...}，于是可写出更为简单的代码：

```cpp
int jumpFloorII(int number) {
    if (number == 0 || number == 1)
        return 1;
    return pow(2, number-1);
}
```



## 矩形覆盖

### 题目描述

我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

比如n=3时，2*3的矩形块有3种覆盖方法：

![img](https://uploadfiles.nowcoder.com/images/20200218/6384065_1581999858239_64E40A35BE277D7E7C87D4DCF588BE84)

### 思路及代码

观察，当n=1时，有1种方法，当n=2时，有2种方法，当n=3时，有3种方法，当n等于4时有5种方法。因此，f[n]=f[n-1]+f[n-2]还是斐波那契问题。

```cpp
int rectCover(int number) {
    if (number == 1 || number == 2)
        return number;
    int a = 1, b = 2, c;
    for (int i = 3; i<=number; ++i)
    {
        c = a + b;
        a = b;
        b = c;
    }
    return c;
}
```

## 二进制种1的个数

### 题目描述

输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示。

### 思路及代码

用1依次向左移位0~31位与输入进行按位与操作。如果结果不等于0，则计数器++

```cpp
int  NumberOf1(int n) {
    int count = 0;
    for (int i = 0; i < 32; ++i){
        if (((1<<i) & n) != 0)
            ++count;
    }
    return count;
}
```

## 数值的整数次方

### 题目描述

给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。保证base和exponent不同时为0。

### 思路及代码

注意exponent可能为负即可。

```cpp
double Power(double base, int exponent) {
    if (exponent == 0)
        return 1;
    if (base == 0)
        return 0;
    double temp = 1;
    for (int i = 0; i < abs(exponent); ++i)
        temp *= base;
    return exponent > 0 ? temp : 1 / temp;
}
```

## 调整数组顺序使奇数位于偶数前面

### 题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

### 思路及代码

1. 使用辅助数组

```cpp
void reOrderArray(vector<int> &array) {
    vector<int> arr;
    for (const int v : array) {
        if (v&1) arr.push_back(v); // 奇数
    }
    for (const int v : array) {
        if (!(v&1)) arr.push_back(v); // 偶数
    }
    copy(arr.begin(), arr.end(), array.begin());
}
```

2. 就地操作

```cpp
void reOrderArray(vector<int> &array) {
    int i = 0;
    for (int j=0; j<array.size(); ++j) {
        if (array[j]&1) {
            int tmp = array[j];
            for (int k=j-1; k>=i; --k) {
                array[k+1] = array[k];
            }
            array[i++] = tmp;
        }
    }
}
```

## 链表中倒数第k个节点

### 题目描述

输入一个链表，输出该链表中倒数第k个结点。

### 思路及代码

这道题不难，难点是要考虑周全，比如：传进来的是空链表，但是k>0；或者链表长度不足k个。

```cpp
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
    if (!pListHead || k<=0)
        return NULL;
    ListNode* pre = pListHead, *curr = pListHead;
    unsigned int count= 0;
    while (curr->next)
    {
        if (count < k-1)
            curr = curr->next;
        else
        {
            curr = curr->next;
            pre = pre->next;
        }
        count++;
    }
    if (count < k-1)
        return NULL;
    return pre;   
}
```

## 反转链表

### 题目描述

输入一个链表，反转链表后，输出新链表的表头。

### 思路及代码

三个指针。

```cpp
ListNode* ReverseList(ListNode* pHead) {
    ListNode *pre = nullptr;
    ListNode *cur = pHead;
    ListNode *nex = nullptr; // 这里可以指向nullptr，循环里面要重新指向
    while (cur) {
        nex = cur->next;
        cur->next = pre;
        pre = cur;
        cur = nex;
    }
    return pre;
}
```

## 合并两个排序的链表

### 题目描述

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

### 思路及代码

值得注意的有两点：

1. 为了保证操作的一致性，需要建立一个哨兵节点。
2. 一共需要4个指针，第一个始终指向头节点，第二个指向当前节点，第三个和第四个分别负责遍历两个链表。

```cpp
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
{
    ListNode *vhead = new ListNode(-1);
    ListNode *cur = vhead;
    while (pHead1 && pHead2) {
        if (pHead1->val <= pHead2->val) {
            cur->next = pHead1;
            pHead1 = pHead1->next;
        }
        else {
            cur->next = pHead2;
            pHead2 = pHead2->next;
        }
        cur = cur->next;
    }
    cur->next = pHead1 ? pHead1 : pHead2;
    return vhead->next;
}
```

## 树的子结构

### 题目描述

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

### 思路及代码

子结构：B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

```
     3
    / \
   4   5
  / \
 1   2
```

给定的树 B：

```
   4 
  /
 1
```

返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

这里需要两个递归函数，第一个负责对比根节点数值和递归对比左子树、右子树数值。加入r2为空，则说明对比结束，返回true，如果r2不为空，但r1为空，则返回false，否则继续向下对比。

第二个递归函数，负责确定从树A的哪个节点开始与树B的根节点进行对比。

```cpp
bool dfs(TreeNode *r1, TreeNode *r2) {
    if (!r2) return true;
    if (!r1) return false;
    return r1->val==r2->val && dfs(r1->left, r2->left) && dfs(r1->right, r2->right);
}

bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
{
    if (!pRoot1 || !pRoot2) return false;
    return dfs(pRoot1, pRoot2) || HasSubtree(pRoot1->left, pRoot2) ||
        HasSubtree(pRoot1->right, pRoot2);

}
```

## 二叉树的镜像

### 题目描述

操作给定的二叉树，将其变换为源二叉树的镜像。

### 思路及代码

```
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

看例子我们很容易看出是左子树和右子树对调。于是有递归函数。

```cpp
void Mirror(TreeNode *pRoot) {
    if (!pRoot)
        return;
    TreeNode* temp = pRoot->left;
    pRoot->left = pRoot->right;
    pRoot->right = temp;
    Mirror(pRoot->left);
    Mirror(pRoot->right);
}
```

## 顺时针打印矩阵

### 题目描述

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 

```
1  2  3  4
5  6  7  8 
9  10 11 12 
13 14 15 16
```

 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

### 思路及代码

重要的是找准边界条件。需要注意的是有可能会多走一行或一列。

```cpp
vector<int> printMatrix(vector<vector<int>> matrix)
{
    vector<int> array;
    if (!matrix.size() || !matrix[0].size())
        return array;
    int row_end = matrix.size() - 1, col_end = matrix[0].size() - 1;
    int row_start = 0, col_start = 0;
    while (row_start <= row_end && col_start <= col_end)
    {
        for (int i = col_start; i <= col_end; ++i)
            array.push_back(matrix[row_start][i]);
        ++row_start;
        if (row_start > row_end)
            break;
        for (int i = row_start; i <= row_end; ++i)
            array.push_back(matrix[i][col_end]);
        --col_end;
        if (col_start > col_end)
            break;
        for (int i = col_end; i >= col_start; --i)
            array.push_back(matrix[row_end][i]);
        --row_end;
        if (row_start > row_end)
            break;
        for (int i = row_end; i >= row_start; --i)
            array.push_back(matrix[i][col_start]);
        ++col_start;
        if (col_start > col_end)
            break;
    }
    return array;
}
```

## 包含min函数的栈

### 题目描述

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

### 思路及代码

因为要求min函数时间复杂度为o（1），于是采用双栈法。如果压入的新值大于辅助栈的栈顶值，则再次压入栈顶的值，否则压入新值。

```cpp
stack <int> normalval, minval;
void push(int value) {
    normalval.push(value);
    if (minval.empty())
        minval.push(value);
    else{
        if (minval.top() < value)
            minval.push(minval.top());
        else 
            minval.push(value);
    }
}
void pop() {
    normalval.pop();
    minval.pop();
}
int top() {
    return normalval.top();
}
int min() {
    return minval.top();
}
```

## 栈的压入、弹出序列

### 题目描述

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

### 思路及代码

进行实际模拟。思路如下图：

![图片说明](https://uploadfiles.nowcoder.com/images/20200419/284295_1587300920579_C29DD1A7B53A19CC1A8C1045C55DDF65)

```cpp
bool IsPopOrder(vector<int> pushV,vector<int> popV) {
    stack <int> st;
    int size = pushV.size();
    int i = 0, j = 0;
    while (i < size)
    {
        if (pushV[i] != popV[j])
            st.push(pushV[i++]);
        else
        {
            ++i,++j;
            while(!st.empty() && st.top()==popV[j])
            {
                st.pop();
                ++j;
            }
        }
    }
    return st.empty();
}
```

## 从上往下打印二叉树

### 题目描述

从上往下打印出二叉树的每个节点，同层节点从左至右打印。考察树的层次遍历，需要借助队列。

1. 初始化：一个队列queue<TreeNode*> q， 将root节点入队列q
2. 如果队列不空，做如下操作：
3. 弹出队列头，保存为node，将node的左右非空孩子加入队列
4. 做2,3步骤，知道队列为空

### 思路及代码

```cpp
    vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> array;
        if (!root)
            return array;
        deque<TreeNode*> dee;
        dee.push_back(root);
        while(!dee.empty())
        {
            TreeNode* temp = dee.front();
            array.push_back(temp->val);
            dee.pop_front();
            if (temp->left)
                dee.push_back(temp->left);
            if (temp->right)
                dee.push_back(temp->right);
        }
        return array;
    }
```

## 二叉搜索树的后序遍历序列

### 题目描述

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则返回true,否则返回false。假设输入的数组的任意两个数字都互不相同。

### 思路及代码

二叉搜索树的后序遍历有个特点就是，左右根且左>右>根。于是数组的最后一个就是根，然后从左向右找第一个大于根的，这个数到根之前的一个数就是根的右子树。判断的条件是右子树中不能有比根小的数。然后遍历就完事了。

```cpp
bool help(int l, int r, const vector<int>& sequence)
{
    if (l >= r)
        return true;
    int k = l;
    while(k < r && sequence[k] < sequence[r])
        ++k;
    for (int i = k; i < r;++i)
    {
        if (sequence[i] < sequence[r])
            return false;
    }
    return help(l, k-1, sequence) && help(k, r-1, sequence);
}

bool VerifySquenceOfBST(vector<int> sequence) {
    if (sequence.empty())
        return false;
    return help(0, sequence.size() - 1, sequence);
}
```

## 二叉树中和为某一值的路径

### 题目描述

输入一颗二叉树的根节点和一个整数，按字典序打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

### 思路及代码

要得到所有路径，必然要遍历整个树，而对于这道路径问题，无疑先序遍历是最合适的，因为从根节点开始。值得注意的是，将sum作为递归函数的一个参数考虑进去。

```cpp
using vvi = vector<vector<int>>;
using vi = vector<int>;
void dfs(TreeNode *root, int sum, vi &path, vvi &ret) {
    path.push_back(root->val);
    if (sum == root->val && !root->left && !root->right) {
        ret.push_back(path);
    }
    if (root->left) dfs(root->left, sum - root->val, path, ret);
    if (root->right) dfs(root->right, sum - root->val, path, ret);
    path.pop_back(); // 代码走到这里，表明要回溯，代表当前path中的root节点我已经不需要了
}
vector<vector<int> > FindPath(TreeNode* root,int expectNumber) {
    vvi ret;
    vi path;
    if (!root) return ret;
    dfs(root, expectNumber, path, ret);
    sort(ret.begin(), ret.end());
    return ret;
}
```

## 复杂链表的复制

### 题目描述

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

### 思路及代码

遍历一次旧链表，同时创建新链表，用一个Map保存旧链表和新链表的节点地址对应关系。再遍历一次旧链表，同时根据对应关系给新链表每个节点的next和random成员赋值。
本题一共需要三个辅助指针，一个用来固定指向新链表的头，一个用来遍历旧链表，一个用来遍历新链表。

时间复杂度：O(n)
空间复杂度：O(n)

```cpp
RandomListNode* Clone(RandomListNode* pHead)
{
    if (!pHead)
        return NULL;
    RandomListNode* target = new RandomListNode(pHead->label);

    RandomListNode* cur = pHead, *p = target;

    map<RandomListNode*, RandomListNode*> indexMap;

    while(pHead)
    {
        indexMap[pHead] = new RandomListNode(pHead->label);
        pHead = pHead->next;
    }

    while(cur)
    {
        p->next = indexMap[cur->next];
        p->random = indexMap[cur->random];
        p = p->next;
        cur = cur->next;
    }

    return target;
}
```

## 二叉搜索树与双向链表

### 题目描述

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

### 思路及代码

用一个辅助数组把中序遍历的结果保存下来，然后处理这个链表即可。

```cpp
void dfs(TreeNode* curr, vector<TreeNode*>& array)
{
    if (!curr)
        return;
    dfs(curr->left, array);
    array.emplace_back(curr);
    dfs(curr->right, array);
}

TreeNode* Convert(TreeNode* pRootOfTree)
{

    if (!pRootOfTree)
        return NULL;
    vector<TreeNode*> array;
    dfs(pRootOfTree, array);
    for (int i = 0; i< array.size() - 1;++i)
    {
        array[i]->right = array[i+1];
        array[i + 1]->left = array[i];
    }
    return array[0];
}
```

## 字符串的排列

### 题目描述

输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则按字典序打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

### 思路及代码

交换第一个与后面的各个位置的字符，然后固定第一个字符，交换第二个与后面各个位置的字符，递归，set可以用来消除重复。

```cpp
vector<string> Permutation(string str) {
    if (str.empty())
        return {};
    set<string> ret;
    perm(0, str, ret);
    return vector<string>(ret.begin(), ret.end());
}

void perm(int pos, string s, set<string> &ret)
{
    if (pos + 1 == s.length())
    {
        ret.insert(s);
        return ;
    }

    for (int i = pos; i < s.length(); ++i)
    {
        swap(s[pos], s[i]);
        perm(pos+1, s, ret);
        swap(s[pos], s[i]);
    }
}
```

## 数组中出现次数超过一半的数字

### 题目描述

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

### 思路及代码

1. 辅助unorded_map法

```cpp
int MoreThanHalfNum_Solution(vector<int> numbers) {
    unordered_map<int,int> mp;
    for (const int val : numbers)
    {
        if (++mp[val] > numbers.size() >> 1)
            return val;
    }
    return 0;
}
```

## 最小的k个数

### 题目描述

输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。

### 思路及代码

1. 使用set()函数去重并排序，然后取出前k个。

```cpp
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {

    vector<int> result;
    if (k > input.size())
        return result;
    set<int> s;
    s.insert(input.begin(), input.end());
    auto it = s.begin();
    for (int i = 0; i < k; ++i)
    {
        result.push_back(*it);
        ++it;
    }
    return result;
}
```

2. 使用大根堆，如果val比最大的小，就入堆，最大的出堆

```cpp
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
    vector<int> ret;
    if (k==0 || k > input.size()) return ret;
    priority_queue<int, vector<int>> pq;
    for (const int val : input) {
        if (pq.size() < k) {
            pq.push(val);
        }
        else {
            if (val < pq.top()) {
                pq.pop();
                pq.push(val);
            }

        }
    }

    while (!pq.empty()) {
        ret.push_back(pq.top());
        pq.pop();
    }
    return ret;
}
```

3. 对数组[l, r]一次快排partition过程可得到，[l, p), p, [p+1, r)三个区间,[l,p)为小于等于p的值
   [p+1,r)为大于等于p的值。
   然后再判断p，利用二分法
   1. 如果[l,p), p，也就是p+1个元素（因为下标从0开始），如果p+1 == k, 找到答案
      2。 如果p+1 < k, 说明答案在[p+1, r)区间内，
      3， 如果p+1 > k , 说明答案在[l, p)内

```cpp
int partition(vector<int> &input, int l, int r) {
    int pivot = input[r-1];
    int i = l;
    for (int j=l; j<r-1; ++j) {
        if (input[j] < pivot) {
            swap(input[i++], input[j]);
        }
    }
    swap(input[i], input[r-1]);
    return i;

}
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
    vector<int> ret;
    if (k==0 || k > input.size()) return ret;
    int l = 0, r = input.size();
    while (l < r) {
        int p = partition(input, l, r);
        if (p+1 == k) {
            return vector<int>({input.begin(), input.begin()+k});
        }
        if (p+1 < k) {
            l = p + 1;
        }  
        else {
            r = p;
        }

    }
    return ret;
}
```



## 连续子数组的最大和

### 题目描述

HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

### 思路及代码

1. 动态规划：

状态定义：dp[i]表示以i结尾的连续子数组的最大和。所以最终要求dp[n-1]
状态转移方程：dp[i] = max(array[i], dp[i-1]+array[i])
解释：如果当前元素为整数，并且dp[i-1]为负数，那么当然结果就是只选当前元素
技巧：这里为了统一代码的书写，定义dp[i]表示前i个元素的连续子数组的最大和，结尾元素为array[i-1]

```cpp
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        int sz = array.size();
        vector<int> dp(sz+1, 1);
        dp[0] = 0; // 表示没有元素
        int ret = array[0];
        for (int i=1; i<=sz; ++i) {
            dp[i] = max(array[i-1], dp[i-1]+array[i-1]);
            ret = max(ret, dp[i]);
        }
        return ret;
    }
};
```

2. 思想很简单，就是对下标为i的元素array[i]，先试探的加上array[i], 如果和为负数，显然，以i结尾的元素对整个结果不作贡献。
   具体过程：
   1. 初始化：维护一个变量tmp = 0
   2. 如果tmp+array[i] < 0, 说明以i结尾的不作贡献，重新赋值tmp = 0
   3. 否则更新tmp = tmp + array[i]
      最后判断tmp是否等于0， 如果等于0， 说明数组都是负数，选取一个最大值为答案。

```cpp
int FindGreatestSumOfSubArray(vector<int> array) {
    int ret = array[0];
    int tmp = 0;
    for (const int k : array) {
        if (tmp + k < 0) {
            tmp = 0;
        }
        else {
            tmp += k;
        }
        ret = max(ret, tmp);
    }

    if (tmp != 0)
        return ret;
    return *max_element(array.begin(), array.end());
}
```

## 整数中1出现的次数（从1到n整数中出现1的次数）

### 题目描述

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

### 思路及代码

见：https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/solution/mian-shi-ti-43-1n-zheng-shu-zhong-1-chu-xian-de-2/

下面的代码中，digit代表位置，先是个位，digit=1，然后十位，digit=10，依次递增。

```cpp
int countDigitOne( int n)
{
    int digit = 1, res = 0;
    int high = n / 10, cur = n% 10, low = 0;
    while (high!=0 || cur != 0)
    {
        if (cur == 0)
            res += high * digit;
        else if (cur == 1) res += high * digit + low + 1;
        else res += (high + 1) * digit;
        low += cur * digit;
        cur = high % 10;
        high /= 10;
        digit *= 10;
    }
    return res;
}
```

## 把数组排成最小的数

### 题目描述

输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

### 思路及代码

首先将int转为string，然后使用贪心算法：

如果a+b<b+a，则将a+b排在前面。

```cpp
string PrintMinNumber(vector<int> numbers) {
    vector <string> str;
    for (const int i : numbers) str.push_back(to_string(i));
    sort(str.begin(), str.end(), [](string a, string b){
        return a + b < b+ a;
    });
    string ret;
    for (const string s : str)
        ret += s;
    return ret;
}
```

## 丑数

### 题目描述

把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

### 思路及代码

丑数除了1，2，3，5之外，别的丑数都等于这四个丑数互相乘。但是难点在于找出每次递增后最小的那个丑数，于是可以用三个指针，如果result[p2]位置的数已经乘过了2，就将p2++。这样做的好处是不但可以去重，而且还排好了序。

```cpp
int GetUglyNumber_Solution(int index) {
    if (index <= 0)
        return 0;
    int p2 = 0, p3 = 0, p5 = 0;      // 用来指向下一次该result的哪个位置乘2，乘3或乘5了。
    int* result = new int[index];
    result[0] = 1;
    for (int i = 1; i< index; ++i)
    {
        result[i] = min(result[p2]*2, min(result[p3]*3, result[p5]*5));
        if (result[i] == result[p2]*2) p2++;
        if (result[i] == result[p3]*3) p3++;
        if (result[i] == result[p5]*5) p5++;
    }
    return result[index-1];
}
```

## 第一次只出现一次的字符

### 题目描述

在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.（从0开始计数）

### 思路及代码

1. 先用哈希表保存每个字符出现的字数，然后再遍历一次字符串，找出第一个出现次数为1的字符。

```cpp
int FirstNotRepeatingChar(string str) {
    if (str.empty()) return -1;
    unordered_map<char, int> charFreq;
    for (const char c : str)
    {
        ++charFreq[c];
    }
    for (int i = 0; i < str.size(); ++i)
    {
        if (charFreq[str[i]] == 1)
            return i;
    }
    return -1;
}
```

2. 使用bitset，原理基本同上，但是占用更少的空间，这里选128位是包含了常用的字符ASCII编码。其中`^`为异或操作。

```cpp
int FirstNotRepeatingChar(string str) {
    bitset<128> b1, b2;
    for (const int ch : str){
        if (!b1[ch] && !b2[ch])
            b1[ch] = 1;
        else if (b1[ch] && !b2[ch])
            b2[ch] = 1;
    }
    for(int i = 0; i < str.size(); ++i)
    {
        if ((b1[str[i]] ^ b2[str[i]]))
            return i;
    }
    return -1;
}
```

## 数组中的逆序对

### 题目描述

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

### 思路及代码

1. 暴力法，从数组中第一个数开始，固定先找第一个数的所有逆序对，然后第二个，最后倒数第二个

```cpp
int InversePairs(vector<int> data) {
    int ret = 0;
    for (int i = 0; i < data.size()-1; ++i)
    {
        for (int j = i+1; j < data.size(); ++j)
        {
            if (data[j] < data[i])
                ++ret;
        }
    }
    return ret % 1000000007;
}
```

2. 上面的方法如果超时的话，就是用二路归并算法，思想可以参考：https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/solution/shu-zu-zhong-de-ni-xu-dui-by-leetcode-solution/

```cpp
class Solution {
    private:
    const int kmod = 1000000007;
    public:
    int InversePairs(vector<int> data) {
        int ret = 0;
        // 在最外层开辟数组
        vector<int> tmp(data.size());
        merge_sort__(data, tmp, 0, data.size() - 1, ret);
        return ret;
    }

    void merge_sort__(vector<int> &arr, vector<int> &tmp, int l, int r, int &ret) {
        if (l >= r) {
            return;
        }

        int mid = l + ((r - l) >> 1);
        merge_sort__(arr, tmp, l, mid, ret);
        merge_sort__(arr, tmp, mid + 1, r, ret);
        merge__(arr, tmp, l, mid, r, ret);
    }

    void merge__(vector<int> &arr, vector<int> &tmp, int l, int mid, int r, int &ret) {
        int i = l, j = mid + 1, k = 0;

        while (i <= mid && j <= r) {
            if (arr[i] > arr[j]) {
                tmp[k++] = arr[j++];
                ret += (mid - i + 1);
                ret %= kmod;
            }
            else {
                tmp[k++] = arr[i++];
            }
        }

        while (i <= mid) {
            tmp[k++] = arr[i++];
        }
        while (j <= r) {
            tmp[k++] = arr[j++];
        }

        for (k = 0, i = l; i <= r; ++i, ++k) {
            arr[i] = tmp[k];
        }
    }

};
```

## 两个链表的第一个公共节点

### 题目描述

输入两个链表，找出它们的第一个公共结点。（注意因为传入数据是链表，所以错误测试数据的提示是用其他方式显示的，保证传入数据是正确的）

### 思路及代码

1. 使用Hashmap

```cpp
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        auto p1 = pHead1, p2 = pHead2;
        if (!p1 || !p2)
            return nullptr;
        unordered_map<ListNode*, bool> p1_map;
        while (p1)
        {
            p1_map[p1] = true;
            p1 = p1->next;
        }
        
        while(p2)
        {
            if (p1_map[p2])
                return p2;
            p2 = p2->next;
        }
        return nullptr;
    }
};
```

解法见：https://blog.nowcoder.net/n/a0c6c80ab3134c20885a2a12e7b4282b?f=comment

```cpp
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        auto p1 = pHead1, p2 = pHead2;
        if (!p1 || !p2)
            return nullptr;
        while(p1 != p2)
        {
            p1 = p1 ? p1->next : pHead2;
            p2 = p2 ? p2->next : pHead1;
        }
        return p1;
    }
};
```

## 数字在升序数组中出现的次数

### 题目描述

统计一个数字在升序数组中出现的次数。

### 思路及代码

分别取到上界和下界，然后相减即可。

```cpp
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        if (data.empty())
            return 0;
        int lbound, rbound;
        int l = 0, r = data.size(), mid;
        // 取下界
        while (l < r)
        {
            mid = l + (r - l) / 2;
            if (data[mid] < k)
                l = mid + 1;
            else 
                r = mid;
        }
        lbound = l;
        // 取上界
        l = 0, r = data.size();
        while (l < r)
        {
            mid = l + (r - l) / 2;
            if (data[mid] <= k)
                l = mid + 1;
            else 
                r = mid;
        }
        rbound = r;
        return rbound - lbound;
    }
};
```

或者使用标准库函数

```cpp
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        if (data.empty())
            return 0;
        auto lbound = lower_bound(data.begin(), data.end(), k);
        auto rbound = upper_bound(data.begin(), data.end(), k);
        return rbound - lbound;
    }
};
```

## 二叉树的深度

### 题目描述

输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

### 思路及代码

1. 分治法

```cpp
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if (!pRoot) return 0;
        int lval = TreeDepth(pRoot->left);
        int rval = TreeDepth(pRoot->right);
        return max(lval, rval) + 1;
 
    }
};
```

2. 使用队列层次遍历法(队列每清空一次，相当于遍历了一层。)

```cpp
class Solution {
public:
    int TreeDepth(TreeNode* pRoot)
    {
        if (!pRoot) return 0;
        int ret = 0;
        queue<TreeNode*> q;
        q.push(pRoot);
        while(!q.empty())
        {
            int sz = q.size();
            while (sz--)
            {
                auto p =q.front();
                q.pop();                
                if (p->left)
                    q.push(p->left);
                if (p->right)
                    q.push(p->right);
            }
            ++ret;
        }
         return ret;
    }
};
```

## 平衡二叉树

### 题目描述

输入一棵二叉树，判断该二叉树是否是平衡二叉树。

在这里，我们只需要考虑其平衡性，不需要考虑其是不是排序二叉树

### 思路及代码

采用后序遍历，自底向上的方法，具体看：https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/solution/mian-shi-ti-55-ii-ping-heng-er-cha-shu-cong-di-zhi/

```cpp
class Solution
{
public:
    bool IsBalanced_Solution(TreeNode *pRoot) { return dfs(pRoot) != -1; }

    int dfs(TreeNode *root)
    {
        if (!root) return 0;
        int left = dfs(root->left);
        if (left == -1) return -1;
        int right = dfs(root->right);
        if (right == -1) return -1;
        return abs(right - left) > 1 ? -1 : max(right, left) + 1;
    }
};
```

## 数组中只出现一次的数字

### 题目描述

一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

### 思路及代码

异或，第一次将整个数组异或，相同的值异或会等于0，所以最后的结果是两个不同的值的异或。紧接着，使用`ret = ret & (-ret)`即可取出ret最右边为1的位。这是因为，`-ret`的结果是所有二进制位取反然后+1。那两个不同的数，此二进制位必定不同。然后再次遍历数组。用这个数`&`再次`^`元素，如果为真，则将异或的结果存到`*num1`，否则存到`*num2`。

```cpp
class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        int ret;
        *num1 = 0, *num2 = 0;
        for (const int val : data)
            ret ^= val;
        ret = ret & (-ret);
        for (const int val : data)
        {
            if (val & ret) *num1 = val ^ *num1;
            else *num2 = val ^ *num2;
        }
    }
};
```

## 和为S的连续正数序列

### 题目描述

小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

### 思路及代码

1. 遍历法

```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> result;
        for (int i = 1; i <= sum / 2; ++i)
        {
            int temp = 0;
            for (int j = i; j < sum; ++j)
            {
                temp += j;
                if (temp == sum)
                {
                    vector<int> array;
                    for (int k = i; k <= j; ++k)
                        array.push_back(k);
                    result.push_back(array);
                }
                else if (temp>sum)
                    break;
            }
            temp = 0;
        }
        return result;
    }
};
```

2. 滑动窗口法
   1. 滑动窗口的操作

- 扩大窗口，`j += 1`
- 缩小窗口，`i += 1`

![图片说明](https://uploadfiles.nowcoder.com/images/20200504/284295_1588586066412_7E35C7D253FD76C798B44ABB19E885BF)



```cpp
class Solution
{
public:
    vector<vector<int>> FindContinuousSequence(int sum)
    {
        vector<vector<int>> result;
        int i = 1, j = 1;
        int temp = 0;
        while (i <= sum / 2)
        {
            if (temp == sum)
            {
                vector<int> array;
                for (int l = i; l < j; ++l) { array.push_back(l); }
                result.push_back(array);

                temp -= i;
                ++i;
            }
            else if (temp < sum)
            {
                temp += j;
                ++j;
            }
            else
            {
                temp -= i;
                ++i;
            }
        }
        return result;
    }
};
```

## 和为S的两个数字

### 题目描述

输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

### 思路及代码

双指针

```cpp
class Solution
{
public:
    vector<int> FindNumbersWithSum(vector<int> array, int sum)
    {
        vector<int> result;
        int l = 0, r = array.size() - 1;
        while (l < r)
        {
            int temp = array[l] + array[r];
            if (temp > sum) --r;
            else if (temp < sum)
                ++l;
            else
            {
                result.push_back(array[l]);
                result.push_back(array[r]);
                return result;
            }
        }
        return result;
    }
};
```

## 左旋转字符串

### 题目描述

汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

### 思路及代码

使用标准库。

```cpp
class Solution {
public:
    string LeftRotateString(string str, int n) {
        if (!sz) return "";
        n = n%sz;
        if (n > str.size()) return str;
        return str.substr(n) + str.substr(0, n);
    }
};
```

## 反转单词顺序列

### 题目描述

牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

### 思路及代码

注意`substr()`方法的第二个参数，不是第二个数字的下标，而是距离、个数。

```cpp
class Solution
{
public:
    string ReverseSentence(string str)
    {
        if (!str.size()) return "";
        string nStr;
        int sz = str.size();
        nStr.reserve(sz);
        for (int l = sz - 1, r = sz; l >= 0; --l)
        {
            if (str[l] == ' ')
            {
                for (int i = l + 1; i < r; ++i) nStr += str[i];
                r = l;
                nStr += ' ';
            }
            else if (l == 0)
            {
                for (int i = l; i < r; ++i) nStr += str[i];
            }
        }
        return nStr;
    }
};
```

## 扑克牌顺子

### 题目描述

LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

### 思路及代码

先排序，一个大小王可以弥补1-3之间2个距离。入宫出现两张相同的非王牌，直接false。

```cpp
class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        if (numbers.empty())
            return false;
        sort(numbers.begin(), numbers.end());
        int temp = 0, zero_numbers = 0;
        for (int i = 0; i < numbers.size(); ++i)
        {
            if (numbers[i]==0)
                ++zero_numbers;
            else
                break;
        }
        for (int i = zero_numbers; i < numbers.size() - 1; ++i)
        {
            if (numbers[i] == numbers[i+1])
                return false;
            temp += numbers[i+1] - numbers[i] - 1;
        }
        return temp<=zero_numbers;
    }
};
```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```

## 

### 题目描述



### 思路及代码



```cpp

```
