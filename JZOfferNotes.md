[TOC]

### Pieces
![avatar](/Img/1.png)

### 剑指Offer

#####  JZ7 斐波那契数列 数组:
斐波那契数列来源于兔子繁殖问题，所以也叫兔子序列。第一年有一对小兔子，一年后成年。成年的兔子又可以生出一对小兔子，如此循环往复，每年的兔子数就构成了一个斐波那契数列。variant: 爬楼梯问题，一次可以爬一级，也可以爬两级 
Fibonacci: 1, 1, 2, 3, 5, 8, 13..., F(n) = F(n-1) + F(n-2)
+ solution1:
```python
# -*- coding:utf-8 -*-
class Solution:
    def Fibonacci(self, n):
        # write code here
        # F(0)=0, F(1)=1, F(2)=1, F(3)=2, F(4)=3, F(5)=8,..., F(n)=F(n-1)+F(n-2)
        res = [0, 1, 1]
        while(len(res) < n+1):
            res.append(res[-1] + res[-2]) # 列表中最后两个元素相加
        return res[n]
```
+ solution2:
```python
# -*- coding:utf-8 -*-
class Solution:
    def Fibonacci(self, n):
        # write code here
        # F(0)=0, F(1)=1, F(2)=1, F(3)=2, F(4)=3, F(5)=8,..., F(n)=F(n-1)+F(n-2)
        if n==0:
            return 0
        if n==1 or n==2:
            return 1
        f1, f2, total = 1, 1, 0
        for i in range(3, n+1): # 从 n=3 开始向上相加 
            total = f1 + f2
            f1 = f2
            f2 = total
        return total
```

##### JZ5 两个栈实现队列 栈
用**两个栈**来实现一个队列, 完成队列的 Push 和 Pop 操作, 队列中的元素为 int 类型
+ analysis:
队列特点: 先入先出 ; 栈特点: 先入后出
实现方法: 有两个栈 stack1 和 stack2 , stack1 作为插入栈, stack2 作为弹出栈, 两个栈整体看作一个队列结构
各个状态
(1) 向队列中按顺序插入元素 a,b,c 此时就是向 stack1 中插入元素
    stack1：{a,b,c}
    stack2：{}
(2) 此时想从队列中弹出一个元素, 队列特点是先进先出, 因此弹出的元素应该是 a, 但此时 a 在 stack1 的最下面, 因此我们将 stack1 中的全部元素弹出, 再压入 stack2 中. 此时元素 a 就在 stack2 的栈顶, 因此直接弹出就可以. 如果想再次从这个组成的队列中弹出元素, 按照先进先出的远足, 下一个弹出元素是 b, 因此直接从 stack2 中弹出 b 就可以.
    stack1：{}
    stack2：{c,b}
(3) 添加元素 d , 先添加进 stack1 中, 等待 stack2 中元素数量为 0 时, 将 stack1 中的元素弹出压入 stack2.
    stack1：{d}
    stack2：{c}

即: 
当插入时，直接插入 stack1
当弹出时，当 stack2 不为空，弹出 stack2 栈顶元素，如果 stack2 为空，将 stack1 中的全部数逐个出栈入栈 stack2，再弹出 stack2 栈顶元素

conclusion: 用两个栈实现队列结构, 关键在于如何实现这两个栈之间的内部的协作关系, 通俗的说就是倒一下内部的数据元素, 即 弹出元素的时候要先将 stack1 中的元素弹出, 压入 stack2 中, 再从 stack2 中弹出. 从 stack2 中弹出即为从队列中弹出元素. 即 添加元素是向 stack1 中添加元素, 弹出元素是从 stack2 中弹出元素. 

+ solution1
```python
# -*- coding:utf-8 -*-
class Solution:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []
    
    def push(self, node):
        # write code here
        self.stack1.append(node)
        
    def pop(self):
        # return xx
        if self.stack2 == []: # 如果 stack2 是空的, 将 stack1 中的元素弹出压入 stack2 中
            while self.stack1:
                self.stack2.append(self.stack1.pop()) # 把 stack1 中的元素全都弹出压入 stack2
        return self.stack2.pop() # 将 stack2 中的元素弹出来一个 
```

##### JZ6 旋转数组的最小数字 二分/数组
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转 
输入一个非递减排序的数组的一个旋转，输出旋转数组的**最小元素** 
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0 
+ analysis: 
方法一: 直接遍历所有元素, 找出最小的一个, 时间复杂度为 O(n), 但是此方法没用到附加条件, 不是面试官想要的方法 pass
方法二: 用二分法进行查找, 时间复杂度为 O(logn). 二分查找涉及到三个量 low, high, mid. 我们的目标是找到右边部分的左端点. 如果 mid 落在左半边, 那么 low = mid + 1，否则设置 high = mid, 这样不断缩小范围, 最终就能找到这个最低点. 即: 
    (1) 将这个旋转数组看作是前后两个有序的子数组, 然后设定我们的中间值 mid = (low + high) // 2. 注: 此处的 low high mid 指数组中的 index 而不是每个元素具体的值的大小. 
    `4 5 6 7 1 2 3`
     l     m     h
    (2) 当我们选取的中间值 mid 所对应的值大于 low 所对应的值时, 说明此时 mid 还在第一个有序的数组中, 而我们所要求解的最小值是在**第二个有序数组**的**第一个位置**, 此时也就是在 mid 的后面, 因此我们就将 low 移到 mid 所在的位置.
    `4 5 6 7 1 2 3`
     l     m     h
          lm     h (重新计算 m 所在的位置) (到这一步之后就循环上一个步骤, 得到 l m h 位置如下)
           l m   h
    (3) 当我们的 high 所对应的值大于 mid 所对应的值时, 说明最小值可能是 mid 所对应的值或者在 mid 的前面, 因此我们将 high 移到 mid 所在的位置.
    `4 5 6 7 1 2 3`
     l     m     h
          lm     h 
           l m   h ( 到这一步之后执行步骤(3) )
           l mh (重新计算 m 所在的位置)
    (4) 最终我们的 high 所在的位置就是最小值的位置, 直接返回即可.


```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```
```python

```