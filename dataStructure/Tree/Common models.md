# M1 二叉树最长路径/直径

dfs. 

考虑对于每个root，我们分别得到与其相连的左边最长路径和右边的，之后相加即可.

值得注意的是，最终dfs的结果并不一定就是answer.  要实时维护

```c++
int res1 = 0;  
int diameterOfBinaryTree(TreeNode *root)
{
    maxPath(root);
    return res1;
}

int maxPath(TreeNode *root)
{
// 这里递归结束条件要特别注意：不能是!root(而且不需要判断root为空,因为只有非空才会进入递归)，因为单个节点路径长也是0
    if (!root->left && !root->right)  
        return 0;
    int left = root->left ? maxPath(root->left) + 1 : 0;  //判断左子节点是否为空，从而更新左边最长路径
    int right = root->right ? maxPath(root->right) + 1 : 0;
    res1 = max(res, left + right); //更新全局变量
    return max(left, right);  //返回左右路径较大者
}
```



* **变体：**
  * **二叉树最长同值路径**：对于dfs的left和right，先判断是否与root->val相等，相等则+1，否则为0.

```c++
    int dfs(TreeNode* root) {
        if(root == nullptr) return 0;
        int left_length = dfs(root->left);
        int right_length = dfs(root->right);
        if(root->left&&root->left->val == root->val) left_length +=1;
        else left_length = 0;
        if(root->right&&root->right->val == root->val) right_length +=1;
        else right_length = 0;
        maxans = max(left_length+right_length, maxans);
        return max(left_length,right_length);
    }
```



# M2 二叉树各序遍历2求1

