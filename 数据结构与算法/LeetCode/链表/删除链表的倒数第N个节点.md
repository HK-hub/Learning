### 删除链表的倒数第N个节点

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

 

示例 1：


输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
示例 2：

输入：head = [1], n = 1
输出：[]
示例 3：

输入：head = [1,2], n = 1
输出：[1]


提示：

链表中结点的数目为 sz
1 <= sz <= 30
0 <= Node.val <= 100
1 <= n <= sz

进阶：你能尝试使用一趟扫描实现吗？



### 解法一：双指针法

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        // 使用双链表：让first 指针先走 n 步，之后再让 first 指针和 second 指针一起走，这样当first 指针走了到末尾之后，刚好 second 指针就处于倒数第 n 个节点
        ListNode* t = head ;
        ListNode* first = head ;
        ListNode* second = head ;

        for(int i = 1 ; i <= n ; ++i){
            first = first->next ;
        
        }

        if(first == NULL){
            return head->next ;
        }

        while(first->next){
            first = first->next ;
            second = second->next ;
        }

        // 此时 first 为最后一个节点
        // seconde 为倒数 n 个节点
        second->next = second->next->next ;

        return head ;
    }
};
```

![image-20220705162307701](https://s2.loli.net/2022/07/05/NOahexX3fksDKHB.png)