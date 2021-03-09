## ARTS-Weekly 1

### Algorithm:

https://leetcode-cn.com/problems/linked-list-cycle/ 环形链表

需要我们判断链表是否有环

我们用**快慢指针法**解决这个问题，什么是快慢指针法呢？

就是让一个**慢指针** **slowPoint** 以每秒一步的速度

让一个 **快指针 soonPoint** 以每秒两步的速度 都在同一点前进

如果链表无环 则他们一定会遇到下个节点为 **NULL** 的情况

否则，他们一定会在环里相遇

证明：两人不可能在进环前相遇，在直线跑道有速度差无法追赶，当两人同时进环，由于两人速度差为 2 - 1 = 1, 是环长度的因子 那么两人的相对位置一直在 -1 ,一定有一刻两人相遇

这样既可 时间复杂度 O(N),空间复杂度 O(1) (N为链表长度)

引申证明：可得出入环的节点，即两指针第一次相遇时 让其中一个指针返回起点 两个指针都以 步长为 1 去移动，则会在入口相遇 可作为下周的补充证明

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == NULL) return false;
        ListNode *slowPoint = head;
        ListNode *soonPoint = head;
        if(slowPoint -> next == NULL) return false;
        if(soonPoint -> next == NULL|| soonPoint -> next -> next == NULL) return false;
        slowPoint = slowPoint -> next;
        soonPoint = (soonPoint -> next) ->next;
        return check(slowPoint, soonPoint);
    }
    bool check(ListNode *slowPoint_, ListNode *soonPoint_)
    {
        if(slowPoint_ == soonPoint_) return true;
        ListNode *slowPoint = slowPoint_;
        ListNode *soonPoint = soonPoint_;
        if(slowPoint -> next == NULL) return false;
        if(soonPoint -> next == NULL|| soonPoint -> next -> next == NULL) return false;
        slowPoint = slowPoint -> next;
        soonPoint = (soonPoint -> next) ->next;
        return check(slowPoint, soonPoint);
    }
};
```



### Review:

https://lucasjellema.medium.com/how-to-unit-test-a-sql-query-836a87b311ad

读了这篇短文，短文名为 为你的sql查询建立UT

如果看过《有效的单元测试》，我们了解了测试驱动开发（TDD）的概念

那么为sql建立 UT 本质也是为了写出正确的sql，我们通常喜欢在不简单的sql问题上，先写出多种sql，进行尝试，如果这个数据还没有在线上上线，我们通常没法比较取的数与实际情况，可能需要询问DA去check sql是不是正确

而为sql写UT则是先写好UT，然后将自己的sql跑UT，避免将错误的sql去进行尝试这一环节

而对于写UT 还引出了用在sql中用json去做compare的思路

### Tips:

**用计算机思维去思考你的程序正在做什么**

本周在用抢课脚本抢课的时候，前端页面一直刷不出来，提示 正在加载中 ...而这个脚本（selenium+webdrive 模拟在网页发请求）以前是可以适用的，但是新学期改了一下课程的id，抢课怎么都不成功 这个脚本做的比较 low 日志比较不完全，也没去分析日志，知道程序在那个前端显示 **正在加载中** 的页面抢课失败，惯性思维认为自己应该解决前端页面的问题，于是做了很多努力去满足前端页面显示正常，包括要发POST请求，而原生的selenium+webdrive是不支持POST的，努力了半天以后发现还不适用，原来是改课程ID的时候将list改错了。

如果我们用计算机思维去思考我们程序在前端显示 **正在加载中** 页面的时候，他已经有 get 请求的入口了，他只要在这个页面一直模拟发请求就行了，不需要前端页面就绪，但是此时我带入了自己的思维，所以增加了工作量且分析错误，还没有解决问题，得到启发：我们对于**程序要用计算机思维去思考他在做什么**

### Share:

素数是一个很神奇的数

那么我们有什么办法可以预处理出小于等于某个数的所有素数呢？

原始Check 时间复杂度(O(n^2))

```c++
const int MAX_N = 10025;
bool isPrime[MAX_N];

int main()
{
    
    for(int i = 1;i<=n;++i)
    {
        isPrime[i] = true;
        for(int j = 2;j<i;++j)
        {
            if(i%j==0) isPrime[i] = false;
        }
    }
    return 0;
}
```

后来我们发现除数的性质，即一大一小分布（或两个等于）j 与 i/j (when i%j == 0) 的关系

所以我们只需要枚举 sqrt(i)的范围 

改进版本 时间复杂度为O(n*sqrt(n))

```c++
const int MAX_N = 10025;
bool isPrime[MAX_N];

int main()
{
    
    for(int i = 1;i<=n;++i)
    {
        isPrime[i] = true;
        for(int j = 2;j*j<i;++j)
        {
            if(i%j==0) isPrime[i] = false;
        }
    }
    return 0;
}
```

我们再引入埃式筛，我们发现一个素数，他的任意倍数肯定不是素数 所以每次遇到素数的话 我们进行一次处理 为其所有倍数打上非素数的tag

```c++
const int MAX_N = 10025;
bool prime[MAX_N];

// prime[i] == i 时 i 为素数
int main()
{
    
    for(int i = 1;i<=n;++i)
    {
        if(prime[i]!=0) continue;
        prime[i] = i;
        for(int j = 2;j*i<n;++j)
        {
            prime[j*i] = i;
        }
    }
    return 0;
}
```

时间复杂度为 O(n)+O(n/2) + O(n/3) + O(n/5) + ...  为 O(nlogn)

还有一个 O(n)的线性筛版本 改进埃式筛版本的点为使得每次遍历只被访问一次，例如埃式筛中6被2和3这两个素数访问，在线性筛中只会被2访问，利用了一个素数作为合数因子时当且仅当合数 >= 此素数*此素数 

下周的ARTS Weekly 会更新线性筛