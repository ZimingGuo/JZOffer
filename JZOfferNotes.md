[TOC]

### Pieces
![avatar](xxx)

### 剑指Offer

##### JZ7 斐波那契数列 数组:
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
方法二: 用**二分法**进行查找, 时间复杂度为 O(logn). 二分查找涉及到三个量 low, high, mid. 我们的目标是找到右边部分的左端点. 如果 mid 落在左半边, 那么 low = mid + 1，否则设置 high = mid, 这样不断缩小范围, 最终就能找到这个最低点. 即: 
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
           l mh **(此时 high 和 low 之间的 cursor 的值只差 1, 因此锁定最小值为 high 对应的值)**
          
    (4) 最终我们的 high 所在的位置就是最小值的位置, 直接返回即可.

+ solution
```python
# -*- coding:utf-8 -*-
class Solution:
    def minNumberInRotateArray(self, rotateArray):
        # write code here
        
        # 特殊情况的处理 
        if not rotateArray:
            return 0
        if len(rotateArray) == 1: # 如果数组中只有一个元素, 直接返回此元素
            return rotateArray[0]
        
        # 一般情况的处理
        low = 0
        high = len(rotateArray) - 1
        
        while rotateArray[low] >= rotateArray[high]: # 一旦不符合此条件, 说明 low 已经进入右侧部分
            if high - low == 1: # 当两个元素相邻的时候, 说明 high 对应的元素肯定是最小的元素
                break
            mid = (low + high)//2
            
            # 如果三个值相等: 此时需要在这一段进行顺序查找
            if rotateArray[low] == rotateArray[mid] and rotateArray[high] == rotateArray[mid]:
                temp = rotateArray[low]
                for i in range(low, high + 1):
                    if temp < rotateArray[i]:
                        temp ==rotateArray[i]
                return temp # 循环结束后直接返回最小值
            
            # 如果 mid 对应的值大于 low 的值, 说明此时 mid 在左边部分
            if rotateArray[mid] >= rotateArray[low]:
                low = mid
                
            # 如果 mid 对应的值小于 high 对应的值, 说明此时 mid 在右侧部分
            elif rotateArray[high] >= rotateArray[mid]:
                high = mid
        return rotateArray[high]
```

##### JZ9 变态跳台阶 贪心
一只青蛙一次可以跳上1级台阶, 也可以跳上2级 ... 它也可以跳上n级. 求该青蛙跳上一个n级的台阶总共有多少种跳法
+ analysis:
设 n 级台阶有 f(n) 种跳法, 将 n 级台阶分解为: 先跳 n-1 级, 最后跳一级 / 先跳 n-2级, 最后跳2级 / 先跳 n-3 级, 最后跳三级 ..... 以此类推, 跳上 n 级台阶有跳法数 f(n) 可表示为 f(n) = f(n-1) + f(n-2) + ... + f(2) + f(1). 而 f(n-1) 又可以通过上面的方式分解为 f(n-1) = f(n-2) + f(n-3) ... + f(2) + f(1).
因此, 两式相减得 f(n) = 2f(n-1). 
n = 1时, 只有1种跳法, f(1) = 1
n = 2时, 会有两个跳得方式, 一次1阶或者2阶, 这回归到了问题（1） ，f(2) = f(2-1) + f(2-2) 
n = 3时, 会有三种跳得方式, 1阶 2阶 3阶, 那么就是第一次跳出1阶后面剩下: f(3-1);第一次跳出2阶, 剩下f(3-2); 第一次3阶, 那么剩下f(3-3). 因此结论是f(3) = f(3-1)+f(3-2)+f(3-3)
n = n时, 会有n中跳的方式, 1阶 2阶 ... n阶, 得出结论: 
    f(n) = f(n-1)+f(n-2)+...+f(n-(n-1)) + f(n-n) => f(0) + f(1) + f(2) + f(3) + ... + f(n-1)

由以上已经是一种结论，但是为了简单，我们可以继续简化：
    f(n-1) = f(0) + f(1)+f(2)+f(3) + ... + f((n-1)-1) = f(0) + f(1) + f(2) + f(3) + ... + f(n-2)
    f(n) = f(0) + f(1) + f(2) + f(3) + ... + f(n-2) + f(n-1) = f(n-1) + f(n-1)
    可以得出：
    f(n) = 2*f(n-1)

+ solution 
```python
# -*- coding:utf-8 -*-
class Solution:
    def jumpFloorII(self, number):
        # write code here
        if number<=0:
            return 0
        if number==1:
            return 1
        if number==2:
            return 2
        
        # 当台阶数大于 3 的时候才开始进行此迭代
        res = [1, 2]
        # f[n] = f[n-1] + f[n-2] + ... + f[0]
        # f[n-1] = f[n-2] + f[n-3] + ... + f[0]
        #所以一合并，f[n] = 2*f[n-1]
        for _ in range(2, number):
            res.append(2*res[-1])
         
        return res[-1]
```

##### JZ16 合并两个排序的链表 链表
输入两个单调递增的链表, 输出两个链表合成后的链表, 需要合成后的链表满足单调不减规则
+ analysis 
非递归: 
1) 如果 pHead1 指向的结点值小于等于 pHead2 指向的结点值, 则将 pHead1 指向的结点值链接到 tmp 的 next 指针, 然后 pHead1 指向下一个结点值. 
2) 否则让 pHead2 指向下一个结点值 
3) 循环步骤 1,2，直到 pHead1 或者 pHead2 的 next 为 None
4) 将 pHead1 或者 pHead2 剩下的部分链接到 tmp 的后面
+ solution1: 非递归方法:
明确三个指针(节点): pHead1 为链表1的指针, pHead1 为链表2的指针, tmp 为合并后链表的指针. 通过 pHead1 和 pHead2 比较两个链表中每个元素之间的大小关系, 令合并后链表的指针 tmp 指向小的元素的节点, 并后移三个指针. 具体流程图如下:
![avatar](/Img/JZ16.jpg) 
```python
# -*- coding:utf-8 -*-
# class ListNode: # 初始化节点对象 
#     def __init__(self, x):
#         self.val = x # 此节点的值 
#         self.next = None # 下一个节点的值 
class Solution:
    # 返回合并后列表
    def Merge(self, pHead1, pHead2): # pHead1 链表1中的头指针(节点对象) & pHead2 链表2中的头指针(节点对象) 
        # write code here
        # method01 非递归方法
        # 如果某指针值为空, 则返回另一链表指针
        if pHead1 == None:
            return pHead2
        if pHead2 == None: 
            return pHead1
        
        tmp = ListNode(0) # 建立头节点对象, 合并后链表的第一个元素 
        res = tmp # 合并后的链表的头指针, 做好标记, 方便最终输出结果 
        while pHead1 and pHead2: # 当两个头指针都不为空时, 执行此循环
            if pHead1.val < pHead2.val: # 链表1指针节点的值小于链表2的指针节点的值 
                tmp.next = pHead1 # 将合并链表的头节点挂在链表1的指针节点前 
                pHead1 = pHead1.next # 链表1指针后移一位 (在链表1上后移一位)
            else:
                tmp.next = pHead2
                pHead2 = pHead2.next # 指针后移一位
            tmp = tmp.next # 合并链表的指针也要后移一位 

        # 循环结束, 即 有一个链表已经遍历完毕
        if pHead1: # 如果最终链表1指针节点不为空, 将链表1中剩余元素挂在合并链表后面 
            tmp.next = pHead1
        if pHead2: # 如果最终链表2指针节点不为空, 将链表2中剩余元素挂在合并链表后面 
            tmp.next = pHead2
        return res.next # 合并链表头指针后面挂的就是要输出的排好序的合并链表 
```
+ solution2: 递归方法
递归函数结束的条件是: 其中一个链表遍历到到头了, 然后返回不是 None 的那个链表
递归函数一定是缩小递归区间的, 那么下一步的递归区间是什么? 如果 PHead1 的所指节点值小于等于 pHead2 所指的结点值, 那么 pHead1 后续节点和 pHead2 节点继续递归. 
```python
# -*- coding:utf-8 -*-
# class ListNode: # 初始化节点对象 
#     def __init__(self, x):
#         self.val = x # 此节点的值 
#         self.next = None # 下一个节点的值 
class Solution:
    # 返回合并后列表
    def Merge(self, pHead1, pHead2): # pHead1 链表1中的头指针(节点对象) & pHead2 链表2中的头指针(节点对象) 
        # write code here
        
        # method02 递归方法实现 
        if not pHead1 or not pHead2:
            return pHead1 if not pHead2 else pHead2
        p=None # 创建新的指针节点, 合并后链表的指针
        if pHead1.val>pHead2.val:
            p=pHead2
            p.next=self.Merge(pHead1, pHead2.next)
        else:
            p=pHead1
            p.next=self.Merge(pHead1.next, pHead2)
        return p
```

##### JZ18 二叉树的镜像 树
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