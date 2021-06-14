# 单链表求倒数第N个位置

[返回目录](../01-数据结构与算法.md)

---

## 查找

这个题目的思路与快慢指针相似，我们可以想一下：

如果我们让快指针先走 `n-1` 步后，然后让慢指针出发。快慢指针每次都只移动一个位置，当快指针移动到链表末尾的时候，慢指针是否就正处于倒数第 N 个位置呢。

这里把这两个指针称之为快慢指针是不正确的，因为快慢指针是指一个指针移动的快一个指针移动的慢，而此题中 快指针只是比慢指针先移动了 n-1 个位置而已，移动速度是相同的。

如果上边的讲解不好理解，这里提供另外一种思路，就是想象一下，上述快慢指针的移动过程，是否就相当于一个固定窗口大小为 n 的滑动窗口。

这里应该是以，快慢指针的思路，引导滑动窗口来去理解，会比较好理解一些。


*   n = 1 fast 指针不移动 fast 到达最后一个节点 即 fast.next 的时候 slow 也到达尾部节点满条件
*   n = len fast 指针移动 n-1（len -1 ） 次 fast 到达最后一个节点 slow 位于头节点不变 满足条件 两个临界值均满足我们这种假设。
*   1< n < len 的时候我们假设 n = 2 ，那么 fast 比 slow 先移动一步,也就是窗口大小为 2， 那么当 fast.next = null 即 fast 已经指向链表最后一个节点的时候，slow 就指向了 倒数第二个节点。


代码思路

```java
/**
 * 注意我们一般说倒数第 n 个元素 n 是从 1 开始的
 */
private Node getLastIndexNode(Node head, int n) {

    // 输入的链表不能为空，并且 n 大于0
    if (n < 1 || head == null) { return null; }

    n = 10;
    // 指向头结点
    Node fast = head;
    // 倒数第k个结点与倒数第一个结点相隔 n-1 个位置
    // fast 先走 n-1 个位置
    for (int i = 1; i < n; i++) {
        // 说明还有结点
        if (fast.next != null) {
            fast = fast.next;
        } else {
            // 已经没有节点了，但是i还没有到达k-1说明k太大，链表中没有那么多的元素
            return null;
        }
    }

    Node slow = head;
    // fast 还没有走到链表的末尾，那么 fast 和 slow 一起走，
    // 当 fast 走到最后一个结点即，fast.next=null 时，slow 就是倒数第 n 个结点
    while (fast.next != null) {
        slow = slow.next;
        fast = fast.next;
    }
    // 返回结果
    return slow;
}
```

## 删除

删除与查找类似，核心点在定位位置之后，操作位置的指针。

所以我们删除倒数第 n 个元素就要找到倒数第 n + 1 个元素。然后将倒数第 n + 1个元素 p 的 next 指针 `p.next` 指向 `p.next.next` 。

我们找到倒数第 n 个节点的时候，先让 fast 先走了 n-1 步，那么我们删除倒数第 n 个节点的时候就需要 让 fast 先走 n 步，构建一个 n+1 大小的窗口，然后 fast 和 slow 整体平移到链表尾部，slow 指向的节点就是 倒数第 n+1 个节点。

这里我们还可以使用滑动窗口的思想来考虑临界值：

1.  n = 1 的时候我们需要构建的窗口为 2,也就是当 fast.next = null 的时候 slow 在的倒数第二个节点上，那么可想而知是满足我们的条件的。

2.  当 1 < n < len 的时候我们总是能构建出这样的一个 len + 1大小的窗口，n 最大为 len -1 的时候，slow 位于头节点，fast 位于未节点，删除倒数第 n 个元素，即删除正数第二个节点，slow.next = slow.next.next 即可。

3.  当 n > len 的时候可想而知，我们要找的倒数第 n 个元素不存在，此时返回 头节点就好了

4.  n = len 的时候比较特殊，循环并没有因为倒数第 len 个元素不存在而终止，并进行了 `fast = fast.next;` 循环结束后 fast 指向 null , 且此时 slow 位于头节点，所以我们要删除的节点是头节点，只需要在循环结束后判断 如果 `fast == null` 返回 `head.next` 即可

代码思路

```java
/**
 * 删除倒是第 n 个节点 我们就要找到倒数第 n + 1 个节点， 如果 n > len 则返回原列表
 */
private Node deleteLastNNode(Node head, int n) {

   if (head == null || n < 1) { return head; }

   Node fast = head;

   //注意 我们要构建长度为 n + 1 的窗口 所以 i 从 0 开始
   for (int i = 0; i < n; i++) {
       //fast 指针指向倒数第一个节点的时候，就是要删除头节点
       if (fast == null) {
           return head;
       } else {
           fast = fast.next;
       }
   }

   // 由于 n = len 再循环内部没有判断直接前进了一个节点，临界值 n = len 的时候 循环完成或 fast = null
   if (fast == null){
       return head.next;
   }

   //此时 n 一定是小于 len 的 且 fast 先走了 n 步
   Node pre = head;
   while (fast.next != null) {
       fast = fast.next;
       pre = pre.next;
   }
   pre.next = pre.next.next;

   return head;
}
```