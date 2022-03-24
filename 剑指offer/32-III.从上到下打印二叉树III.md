### 题目：剑指offer 32-III.从上到下打印二叉树III
请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。


例如:

给定二叉树: `[3,9,20,null,null,15,7]`,

![image](https://user-images.githubusercontent.com/41363767/159850014-40041565-0c0d-46b1-81ef-fce1f1f3442a.png)

返回其层次遍历结果：
```
[
  [3],
  [20,9],
  [15,7]
]
```

**提示：**
- 节点总数 <= 1000

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof


### 解题过程
#### 提交1 - 用辅助栈来反序输出
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> lorder;
        if (!root)
            return lorder;
        deque<TreeNode*> queue = { root };
        TreeNode* last = root;
        vector<int> curr_level;
        bool leftToRight = true;
        while (!queue.empty()) {
            int curr_num = queue.size();
            stack<int> stack;
            for (int i = 0; i < curr_num; ++i) {
                TreeNode* current = queue.front();
                if (leftToRight)
                    curr_level.push_back(current->val);
                else
                    stack.push(current->val);
                queue.pop_front();
                if (current->left != NULL)
                    queue.push_back(current->left);
                if (current->right != NULL)
                    queue.push_back(current->right);
            }
            while (!stack.empty()) {
                curr_level.push_back(stack.top());
                stack.pop();
            }
            lorder.push_back(curr_level);
            curr_level.clear();
            leftToRight = !leftToRight;
        }
        return lorder;
    }
};
```
**结果：** 执行用时: 4 ms        内存消耗: 13.1 MB

**分析：**

用bool型变量`leftToRight`交替控制从左到右的打印还是从右到左打印输出。
- 当从左到右打印时，出队列且直接插入结果vector中即可；
- 当从右到左打印时，每次出队列先入栈，当需要换行时，不断出栈并插入结果vector。
每换行一次，`leftToRight`值反转一次。


#### 提交2 - 用双端队列的双侧插入和出队
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> lorder;
        if (!root)
            return lorder;
        deque<TreeNode*> queue = { root };
        TreeNode* last = root;
        vector<int> curr_level;
        bool leftToRight = true;
        while (!queue.empty()) {
            int curr_num = queue.size();
            for (int i = 0; i < curr_num; ++i) {
                if (leftToRight) {
                    TreeNode* current = queue.front();
                    curr_level.push_back(current->val);
                    queue.pop_front();
                    if (current->left != NULL)
                        queue.push_back(current->left);
                    if (current->right != NULL)
                        queue.push_back(current->right);
                }
                else {
                    TreeNode* current = queue.back();
                    curr_level.push_back(current->val);
                    queue.pop_back();
                    if (current->right != NULL)
                        queue.push_front(current->right);
                    if (current->left != NULL)
                        queue.push_front(current->left); 
                }
            }
            lorder.push_back(curr_level);
            curr_level.clear();
            leftToRight = !leftToRight;
        }
        return lorder;
    }
};
```
**结果：** 执行用时: 8 ms        内存消耗: 12.2 MB

**分析：**

建议不要凭空想象如何操纵双端队列，应该举一个足够说明问题的例子，并仔细逐步分析如何操纵双端队列能够保证正确输出。
假设树结构为
```
--------------------------------------

                3
       9                  20
   4        8        15        7
20   30  40   50  60   70   80   90

--------------------------------------
```
```
3   l -> r

取front 3 (先左后右的右插)                          3

---------------------------------------------------------

9 20    r -> l

取back 20 (先右后左的左插)

15 7 9    r -> l

取back 9 (先右后左的左插)                           20  9

---------------------------------------------------------

4 8 15 7    l -> r

取front 4 (先左后右的右插)

8 15 7 20 30    l -> r

取front 8 (先左后右的右插)

15 7 20 30 40 50    l -> r

取front 15 (先左后右的右插)

7 20 30 40 50 60 70    l -> r

取front 7 (先左后右的右插)                          4 8 15 7

---------------------------------------------------------

20 30 40 50 60 70 80 90    l -> r

取back 90 (先右后左的左插)
······
```

**总结：**

方法一比方法二多用了辅助栈的空间，且多出来需要入栈出栈的操纵。
