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



# M2 二叉树各序遍历二求一

* 已知前序和中序，求后序
* 已知后序和中序，求前序



前序的第一个就是root, 后序的最后一个也是root.  用这个root将中序从中间剖开成两个子树，

再继续搜索 ： 前序的第二个是左子树的root, 后序的倒数第二个是右子树的root.



```c++
// 已知中序、后序，求前序
// luogu P1030
#include <cstdio>
#include <algorithm>
#include <string>
#include <iostream>
#include <string.h>
using namespace std;
string smid,safter,s1,s2;
string solve(string s){
  if(s.length()==0||s.length()==1) return s;
  else  {
    int len=safter.length();
    int i;
    // 在后序中找根，然后分割中序列
    // 如果已知的是前序和中序，则正向遍历前序字符串找根 
    for(i=len-1;i>=0;--i){   
      if(s.find(safter[i])!=safter.npos)
        break;
    }
    int pos=s.find(safter[i]);
    return (char)(safter[i]) + solve(s.substr(0,pos)) + solve(s.substr(pos+1));
  }
}
int main() {
  cin>>smid>>safter;
  cout<<solve(smid);
  return 0;
}
```

# M3 前中  后中   前后序建树



**helper函数的两个边界都是闭区间**



* **已知后序和中序：**
  * 直接用map记录  不需要再线性搜索了
  * 后序的最后一个就是根，其将中序的树分已经不部分. [? ,pos-1] , [pos+1, ?]
  * 而后序的划分则根据中序被划分的长度

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
    map<int,int> mp;
    TreeNode* helper(vector<int>& inorder, vector<int>& postorder,int l1,int r1,int l2,int r2){
        if(r1<l1 || r2<l2) return nullptr;
        TreeNode* root = new TreeNode(postorder[r2]);
        if(l1==r1) return root;
        int pos = 0;
        pos = mp[postorder[r2]];
        root->left = helper(inorder,postorder,l1,pos-1,l2,pos-l1+l2-1);
        root->right = helper(inorder,postorder,pos+1,r1,r2-r1+pos,r2-1);
        return root;
    }

    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        int n = inorder.size();
        for(int i=0;i<n;i++)
            mp[inorder[i]] = i;
        return helper(inorder,postorder,0,n-1,0,n-1);
    }
};
```





* **已知前序和中序：**
  * 思想与前者完全相同

```cpp
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
    map<int,int> mp;
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        for(int i=0;i<n;i++)
            mp[inorder[i]] = i;
        return helper(preorder,inorder,0,n-1,0,n-1);
    }

    TreeNode* helper(vector<int>& preorder, vector<int>& inorder, int l1, int r1, int l2, int r2) {
        if(l1>r1||l2>r2) return nullptr;
        TreeNode* root = new TreeNode(preorder[l1]);
        if(l1==r1) return root;
        int pos = mp[preorder[l1]];
        // 前序的第一个根已经不需要了
        // 这个长度不要硬记，三个边界是可以推出来的，最后一个是根据长度相等算出来的.
        root->left = helper(preorder,inorder,l1+1,pos+l1-l2,l2,pos-1);
        root->right = helper(preorder,inorder,pos+1+r1-r2 ,r1,pos+1,r2);
        return root;
    }
};
```







* **已知前序和后序：**
  * 需要划分

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
    map<int,int> mp;
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        int n = preorder.size();
        for(int i=0;i<n;i++)
            mp[postorder[i]] = i;
        return helper(preorder,postorder,0,n-1,0,n-1);
    }

    TreeNode* helper(vector<int>& preorder, vector<int>& postorder,int l1,int r1,int l2,int r2){
        if(r1<l1 || r2<l2) return nullptr;
        TreeNode* root = new TreeNode(preorder[l1]);
        if(l1==r1) return root;
        int pos = mp[preorder[l1+1]];
        // 前序的第一个和后序的最后一个相同, 所以锁定左子树的根，然后找到对应右子树的区间
        root->left = helper(preorder,postorder, l1+1,pos-l2+l1+1,l2,pos);
        root->right = helper(preorder, postorder,pos-l2+l1+2,r1,pos+1,r2-1);
        return root;
    }
};
```





# M4 对称二叉树

判断一棵二叉树是否轴对称

Leetcode 101

```c++
class Solution {
public:
    bool check(TreeNode* left, TreeNode* right){
        if(!left&&!right) return true;
        if(!left||!right) return false;
        return left->val==right->val && check(left->right,right->left) && check(left->left,right->right); 
    }
    bool isSymmetric(TreeNode* root) {
        return check(root,root);
    }
};
```



# M5 寻找重复的子树

在一棵树中，寻找重复的子树.

指结构、val完全相同.

https://leetcode.cn/problems/find-duplicate-subtrees/

**LeetCode 652**

大致思路是，我们在遍历树的所有子树时，需要记录下已经遍历到子树的信息。对于遍历到的一个node，判断以其为根的子树是否存在过。

但是如何memo存储一棵树呢？序列化！将二叉树序列化为一个唯一表示的string，然后用map记录.

```cpp
class Solution {
public:
    unordered_map<string, int> mp;
    vector<TreeNode*> ans;

    vector<TreeNode*> findDuplicateSubtrees(TreeNode* root) {
        // 要求返回的是子树的根节点.
        dfs(root);
        return ans;
    }

    string dfs(TreeNode *root) {
        if(root==nullptr) return "";
        string tmp = to_string(root->val);
        tmp += "(" + dfs(root->left) + ")" + "(" + dfs(root->right) + ")";
        mp[tmp]++;
        if(mp[tmp]==2){
            ans.push_back(root);
        }
        return tmp;
    }
};
```



# M6 LCA问题

* **简易LCA：**
  * 只是二叉树
  * 只查询两个节点



如果一个root的左部、右部都能p或q，则root本身就是LCA.

若只有一端，那LCA就是左子树找到的LCA.



该函数本身就是找到一棵树中p和q的LCA，所以如果都在一侧的话，那就去找左侧的.

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root==NULL || root->val == p->val || root->val == q->val){
            return root;
        }
        TreeNode* left = lowestCommonAncestor(root->left,p,q);
        TreeNode* right = lowestCommonAncestor(root->right,p,q);
        if(left && right) return root;
        else if(left && !right) return left;
        else return right;
    }
};
```



# M7 修剪二叉搜索树

Lc 669 https://leetcode.cn/problems/trim-a-binary-search-tree/

还就那个递归

```c++
class Solution {
public:
    TreeNode* trimBST(TreeNode* root, int low, int high) {
        if(root==NULL) return NULL;
        if(root->val < low)
            return trimBST(root->right,low,high);
        if(root->val > high)
            return trimBST(root->left,low,high);
        root->left = trimBST(root->left,low,high);
        root->right = trimBST(root->right,low,high);
        return root;
    }
};
```

