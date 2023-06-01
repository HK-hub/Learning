# [剑指 Offer 34. 二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

![image-20220328203425512](https://s2.loli.net/2022/03/28/d7TL1PrjYa3Zl9n.png)

**实例1：**

![img](https://assets.leetcode.com/uploads/2021/01/18/pathsumii1.jpg)

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：[[5,4,11,2],[5,8,4,5]]
```

**实例2：**

![img](https://assets.leetcode.com/uploads/2021/01/18/pathsum2.jpg)

```
输入：root = [1,2,3], targetSum = 5
输出：[]
```

**示例 3：**

```
输入：root = [1,2], targetSum = 0
输出：[]
```



```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<int> temp ;
    int c = 0 ;
    vector<vector<int>> pathSum(TreeNode* root, int target) {

        if(root == nullptr){
            return {} ;
        }
        vector<vector<int>> path = vector<vector<int>>();
        preorder(root,path,target);
        return path;
    }


    // 深度优先遍历
    void preorder(TreeNode* root, vector<vector<int>>& path ,int target){
        // 遍历退出条件: 当前节点是叶子节点
        if(root == nullptr){
            return;
        }
        // 先序遍历
        temp.push_back(root->val);
        if(root->left == nullptr && root->right == nullptr && 
        accumulate(temp.begin(),temp.end(),0) == target){
            // 需要弹出末尾元素
            path.emplace_back(temp);
        }
        preorder(root->left,path,target);
        preorder(root->right,path,target);
        temp.pop_back();
    }


};
```

