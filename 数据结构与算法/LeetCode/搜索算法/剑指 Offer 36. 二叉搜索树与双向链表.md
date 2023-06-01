# [剑指 Offer 36. 二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

为了让您更好地理解问题，以下面的二叉搜索树为例：

![img](https://assets.leetcode.com/uploads/2018/10/12/bstdlloriginalbst.png)

我们希望将这个二叉搜索树转化为双向循环链表。链表中的每个节点都有一个前驱和后继指针。对于双向循环链表，第一个节点的前驱是最后一个节点，最后一个节点的后继是第一个节点。

下图展示了上面的二叉搜索树转化成的链表。“head” 表示指向链表中有最小元素的节点。

![img](https://assets.leetcode.com/uploads/2018/10/12/bstdllreturndll.png)

特别地，我们希望可以就地完成转换操作。当转化完成以后，树中节点的左指针需要指向前驱，树中节点的右指针需要指向后继。还需要返回链表中的第一个节点的指针。



# 题解

![image-20220331091946826](https://s2.loli.net/2022/03/31/LF1nkedPDT2uc94.png)

````c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;

    Node() {}

    Node(int _val) {
        val = _val;
        left = NULL;
        right = NULL;
    }

    Node(int _val, Node* _left, Node* _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
public:

    Node* pre = nullptr ;
    Node* head;

    Node* treeToDoublyList(Node* root) {
        
        // 左指针指向前驱，右指针指向后继
        if(root == nullptr){
            return root ;
        }

        // 中序遍历：pre 表示前一个节点，
        inOrder(root);

        // 最后遍历完后，head 指向第一个节点，pre 指向最后一个节点
        head->left = pre ;
        pre->right = head ;
    
        return head ;
    }

    void inOrder(Node* cur){
        if(cur == NULL)
            return ;

        // 当前节点和前一个节点
        inOrder(cur->left);
        
        // root 表示当前节点，
        if(pre != nullptr){
            // 前驱节点不为空
            pre->right = cur ;
        }else{
            head = cur ;
        }
        cur->left = pre ;
        // 切换当前节点
        pre = cur ;
        inOrder(cur->right) ;
    }

};
````

